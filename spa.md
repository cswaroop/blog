Single Page Applications
------------------------

How to develop complex GUI applications for Browser within Javascript limits.

Traditional GUI applications means (Eclipse SWT, Adobe Flash, Windows WPF)
1. Event loop (dedicated thread to listen the events from OS)
2. Events (low level events like mouse click, key pressed etc.)
3. Event Handlers (call backs that are registered
4. Non-blocking asynchronous worker framework to run potentially GUI-blocking operations.
5. Event subscribe and notify framework
6. Nice MVC framework to seperate the concerns

But Browswers don't have everthing prepackaged to build applications.  Its for `documents`. What we have are

1 Ajax for asynchronous
2.templates instead of sub-windows
3.Roll-your-own MV*



