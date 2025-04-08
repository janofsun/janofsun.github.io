---
title: VS Property Configuration C/C++
date: 2024-01-04 14:08:52
tags: 
- C++
- OpenCV
categories: 
- [Tech]
- [Project, C++]
---

For my own projects, the project should be set up as the following:
<!--more-->
- From terminal add environment values:
```
setx okFP_SDK C:\Program Files\Opal Kelly\FrontPanelUSB\API
setx OPENCV C:\opencv
setx WX C:\wxWidgets-3.2.2.1
```

- Open the Start menu and type “edit ENV” into the search bar.
Tap “Edit environment variables for your account.”. Add to PATH variable directories:
```
%OPENCV%\build\x64\vc16\bin
%OPENCV%\build\x64\vc15\bin
%OPENCV%\src\opencv\build\bin
%OPENCV%\src\opencv\build\3rdparty\ffmpeg
%okFP_SDK%\lib\x64
%WX%\lib\vc14x_x64_dll
```

- Required DLL's
```
okFrontPanel.dll
opencv_videoio_ffmpeg460_64.dll
opencv_videoio_msmf460_64.dll
opencv_world460.dll
wxbase32u_vc14x_x64.dll
wxmsw32u_aui_vc14x_x64.dll
wxmsw32u_core_vc14x_x64.dll
wxmsw32u_html_vc14x_x64.dll
wxmsw32u_propgrid_vc14x_x64.dll
libcurl-x64.dll
exiv2.dll
```

- Set up the project in Visual Studio
```
$(ProjectDir);$(WX)/include/;$(WX)/lib/vc14x_x64_dll/mswud;$(OPENCV)/build/include;$(okFP_SDK)/include;$(ProjectDir)3rd_party;$(ProjectDir)3rd_party/exiv2;$(ProjectDir)3rd_party/thorlabs_pd100;$(ProjectDir)3rd_party/glew/include
```
<p align="center">
<img src="/img/cpp_proj_init/cpp_general.png" width="100%" height="100%">
</p>

<p align="center">
<img src="/img/cpp_proj_init/cpp_lang.png" width="100%" height="100%">
</p>
```
_DEBUG;DEBUG;WXBUILDING;CURL_STATICLIB;%(PreprocessorDefinitions)
```
<p align="center">
<img src="/img/cpp_proj_init/cpp_preprocessor.png" width="100%" height="100%">
</p>
```
$(OPENCV)/build/x64/vc16/lib;$(OPENCV)/build/x64/vc15/lib;$(OPENCV)/build/lib;$(WX)/lib/vc14x_x64_dll;$(okFP_SDK)/lib/x64;$(ProjectDir)3rd_party/curl;$(ProjectDir)3rd_party/exiv2/Debug;$(ProjectDir)3rd_party/thorlabs_pd100;$(ProjectDir)3rd_party/glew/lib
```
<p align="center">
<img src="/img/cpp_proj_init/linker_general.png" width="100%" height="100%">
</p>
```
wxbase32ud.lib;wxbase32ud_net.lib;wxbase32ud_xml.lib;wxmsw32ud_core.lib;wxmsw32ud_adv.lib;wxmsw32ud_aui.lib;wxmsw32ud_gl.lib;wxmsw32ud_html.lib;wxmsw32ud_media.lib;wxmsw32ud_propgrid.lib;wxmsw32ud_qa.lib;wxmsw32ud_ribbon.lib;wxmsw32ud_richtext.lib;wxmsw32ud_stc.lib;wxmsw32ud_webview.lib;wxmsw32ud_xrc.lib;wxexpatd.lib;wxjpegd.lib;wxpngd.lib;wxregexud.lib;wxscintillad.lib;wxtiffd.lib;wxzlibd.lib;shlwapi.lib;oleacc.lib;uxtheme.lib;$(CoreLibraryDependencies)
```
<p align="center">
<img src="/img/cpp_proj_init/linker_input.png" width="100%" height="100%">
</p>
