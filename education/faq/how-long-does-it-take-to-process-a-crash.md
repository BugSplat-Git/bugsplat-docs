# How long does it take to process a crash?

A crash should always appear as soon as it arrives at our site.

Upon arrival, it will appear as 'pending' until our processors run a debugger to analyze the crash and generate the symbolicated call stack. This processing is typically pretty quick, usually just a few seconds; however, it can take longer in some circumstances.  For example, a crash requiring new symbols to be loaded will often take several minutes to process.  However, it's rare for crash processing to take more than 15 minutes.

Once the crash is analyzed, BugSplat will send notifications and create any required defects in your defect tracker.

If you see a problem where your crash isn't processing, try hitting the 'reprocess' button on the crash page.

If that doesn't resolve the issue, please contact support. We'll be happy to investigate!
