# OPenGL学习部分内容
## 关于GLSurfaceView，主要功能：
（1）给OpenGL提供一个用于渲染的线程,防止主线程被堵塞，提高性能。  
（2）提供连续渲染或者按需渲染的能力。分别通过GLSurfaceView.RENDERMODE_CONTINUOUSLY和GLSurfaceView.RENDERMODE_WHEN_DIRTY进行设置，一般使用
 RENDERMODE_WHEN_DIRTY按需渲染，节省GPU资源消耗。  
（3）封装EGL相关调用，简化OpenGL和窗口系统的使用方式。  
