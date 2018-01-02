---
id: 182
title: Of file handles and implicit offsets
date: 2017-11-25T11:10:54+00:00
author: fsgeek
layout: post
guid: https://fsgeek.ca/?p=182
permalink: /2017/11/25/of-file-handles-and-implicit-offsets/
categories:
  - Questions
tags:
  - file descriptor
  - lseek
  - read
---
My current research direction (which is wandering a bit, as is common with research) has forced me to look as some of the vagaries of the POSIX interface.  One of these is this intriguing decision to incorporate a piece of file descriptor specific state for the &#8220;file pointer&#8221; (note that in Windows there is an exact equivalent in the **CurrentByteOffset** of the file handle).

One thing to note about POSIX is that it was not _designed_ initially.  Rather, it captured the state of UNIX systems in the 1980s and codify it.  Thus, rather than _inventing_ this behavior, POSIX (or officially, [IEEE Std. 1003.1-1988](http://ieeexplore.ieee.org/document/182902/))  codified a uniform interface  acceptable to a variety of parties.  Like any standards document, it is a compromise that attempts to mollify a variety of different players.

Here is a version of the Linux in-kernel file structure (from the main linux repository as of this morning):

<pre class="brush: cpp; highlight: [20]; title: ; notranslate" title="">struct file {
	union {
		struct llist_node	fu_llist;
		struct rcu_head 	fu_rcuhead;
	} f_u;
	struct path		f_path;
	struct inode		*f_inode;	/* cached value */
	const struct file_operations	*f_op;

	/*
	 * Protects f_ep_links, f_flags.
	 * Must not be taken from IRQ context.
	 */
	spinlock_t		f_lock;
	enum rw_hint		f_write_hint;
	atomic_long_t		f_count;
	unsigned int 		f_flags;
	fmode_t			f_mode;
	struct mutex		f_pos_lock;
	loff_t			f_pos;
	struct fown_struct	f_owner;
	const struct cred	*f_cred;
	struct file_ra_state	f_ra;

	u64			f_version;
#ifdef CONFIG_SECURITY
	void			*f_security;
#endif
	/* needed for tty driver, and maybe others */
	void			*private_data;

#ifdef CONFIG_EPOLL
	/* Used by fs/eventpoll.c to link all the hooks to this file */
	struct list_head	f_ep_links;
	struct list_head	f_tfile_llink;
#endif /* #ifdef CONFIG_EPOLL */
	struct address_space	*f_mapping;
	errseq_t		f_wb_err;
} __randomize_layout
  __attribute__((aligned(4)));	/* lest something weird decides that 2 is OK */

</pre>

Note the **f_pos **field (which I&#8217;ve highlighted).  This is the file pointer and it allows things like **read** and **write** to work without an explicit offset value.

Here&#8217;s the equivalent structure in Windows 10:

<pre class="brush: cpp; highlight: [22]; title: ; notranslate" title="">typedef struct _FILE_OBJECT {
    CSHORT Type;
    CSHORT Size;
    PDEVICE_OBJECT DeviceObject;
    PVPB Vpb;
    PVOID FsContext;
    PVOID FsContext2;
    PSECTION_OBJECT_POINTERS SectionObjectPointer;
    PVOID PrivateCacheMap;
    NTSTATUS FinalStatus;
    struct _FILE_OBJECT *RelatedFileObject;
    BOOLEAN LockOperation;
    BOOLEAN DeletePending;
    BOOLEAN ReadAccess;
    BOOLEAN WriteAccess;
    BOOLEAN DeleteAccess;
    BOOLEAN SharedRead;
    BOOLEAN SharedWrite;
    BOOLEAN SharedDelete;
    ULONG Flags;
    UNICODE_STRING FileName;
    LARGE_INTEGER CurrentByteOffset;
    __volatile ULONG Waiters;
    __volatile ULONG Busy;
    PVOID LastLock;
    KEVENT Lock;
    KEVENT Event;
    __volatile PIO_COMPLETION_CONTEXT CompletionContext;
    KSPIN_LOCK IrpListLock;
    LIST_ENTRY IrpList;
    __volatile PVOID FileObjectExtension;
} FILE_OBJECT;
typedef struct _FILE_OBJECT *PFILE_OBJECT; 
</pre>

I highlighted the equivalent field for this structure (from **wdm.h** in the Windows 10 WDK).  I spent some time looking through the various fields and my observation is that this is the only piece of implicit user-visible shared _mutable _state.

This actually doesn&#8217;t work in multi-threaded environments (very common these days) if threads use the same file descriptor (file handle in Windows) since it doesn&#8217;t make any sense to arbitrarily interleave reads.  In those environments, you use a different call &#8211; **pread** for POSIX systems, and in Windows it is explicit parameter in the native system call (**NtReadFile** where it is an _optional_ parameter).

This led me to ask the question: **why is this here**?  I haven&#8217;t found a definitive source since this predates the original POSIX specification, but my theory is that it is because it is the only way to properly implement sharing of the file descriptor.  When UNIX added the fork call, one of the characteristics of it was &#8220;inheritance of file descriptors&#8221;.

<pre>The child inherits copies of the parent's set of open file
          descriptors.  Each file descriptor in the child refers to the same
          open file description (see <a href="http://man7.org/linux/man-pages/man2/open.2.html">open(2)</a>) as the corresponding file
          descriptor in the parent.  This means that the two file
          descriptors share open file status flags, file offset, and signal-
          driven I/O attributes (see the description of <b>F_SETOWN </b>and
          <b>F_SETSIG </b>in <a href="http://man7.org/linux/man-pages/man2/fcntl.2.html">fcntl(2)</a>).

(Source: http://man7.org/linux/man-pages/man2/fork.2.html)
</pre>

The status flags describe how the file was opened so they aren&#8217;t changing (immutable). The addition of **F_SETOWN** and **F_SETSIG**
  
is more recent but it does appear to be _explicitly mutable_ state (it allows programmatic changes).

Fork is not the only way that a file descriptor (or file handle). For example, [it can be done](https://stackoverflow.com/questions/28003921/sending-file-descriptor-by-linux-socket/)
  
using UNIX domain sockets on UNIX and Linux systems. Windows provides a system call for doing something similar
  
as well (the documented version is [**ZwDuplicateObject**](https://msdn.microsoft.com/en-us/library/windows/hardware/ff566445(v=vs.85).aspx)).

I&#8217;ve spent time thinking about this and it seems that the reason to maintain this shared state is to ensure
  
that two processes sharing the same file descriptor/handle get the same position pointer value. This
  
then let me to ask _why is this useful_?

I have been able to construct a single scenario in which this is useful: appending to the end of a shared file.
  
Interleaving reads doesn&#8217;t make much sense. Interleaving writes inside the existing boundaries of a file
  
makes even _less_ sense to me. I can construct peculiar scenarios in which I can write applications that
  
explicitly use this feature but they seem artificial.

Writing to a log file at the end seems like it would make sense. But if that&#8217;s my goal, it makes more
  
sense to just use **O_APPEND** mode:

<pre>The file is opened in append mode.  Before each <a href="http://man7.org/linux/man-pages/man2/write.2.html">write(2)</a>, the
              file offset is positioned at the end of the file, as if with
              <a href="http://man7.org/linux/man-pages/man2/lseek.2.html">lseek(2)</a>.  The modification of the file offset and the write
              operation are performed as a single atomic step.

(Source: http://man7.org/linux/man-pages/man2/open.2.html)
</pre>

Thus, this makes me wonder: could we just eliminate this piece of shared state?  I have a _reason_ for asking this question though I will save discussing that for another time.

Preserving the correct behavior for most applications will require fixing things up in the library &#8211; we could eliminate **read** as a system call and provide a library implementation that calls **pread**.

I&#8217;m considering doing that and seeing what breaks. it is more difficult to do that in Windows than in Linux, so I&#8217;m considering starting there.