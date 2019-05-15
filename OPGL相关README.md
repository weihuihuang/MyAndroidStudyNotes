# OPenGL学习部分内容
### 着色器语言学习
https://github.com/wshxbqq/GLSL-Card  

### 关于GLSurfaceView，主要功能：
（1）给OpenGL提供一个用于渲染的线程,防止主线程被堵塞，提高性能。  
（2）提供连续渲染或者按需渲染的能力。分别通过GLSurfaceView.RENDERMODE_CONTINUOUSLY和GLSurfaceView.RENDERMODE_WHEN_DIRTY进行设置，一般使用
 RENDERMODE_WHEN_DIRTY按需渲染，节省GPU资源消耗。  
（3）封装EGL相关调用，简化OpenGL和窗口系统的使用方式。  
 主要重写3个方法：
（1）void onSurfaceCreated(GL10 gl, EGLConfig config); 创建时调用
（2）void onSurfaceChanged(GL10 gl, int width, int height); 在Surface大小变化时调用，如横竖屏变化，以及在在Surface第一次创建时调用。   
（3）void onDrawFrame(GL10 gl); 渲染一帧图像时调用   
