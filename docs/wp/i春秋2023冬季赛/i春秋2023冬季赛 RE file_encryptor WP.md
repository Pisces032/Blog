# RE file_encryptor
32位无壳
> 代码逆向分析领域中，TLS（Thread Local Storage，线程局部存储）回调函数（Callback Function）常用反调试。TLS回调函数的调用运行要先于EP代码的执行，该特征使它可以作为一种反调试技术的使用。

动调不起来，main函数直接爆红<br />![image.png](p/F1.png)<br />肯定有反调试、异常处理<br />main里面没找到，尝试TLS里面找<br />![image.png](p/F2.png)<br />这里看到了`IsDebuggerPresent`在`except`异常处理里<br />改一下控制流，不要去`exit`<br />![image.png](p/F3.png)<br />![image.png](p/F4.png)<br />回到main函数还有一堆异常处理<br />![image.png](p/F5.png)<br />全部滚吧，NOP所有except<br />![image.png](p/F6.png)<br />爆红的花指令修一下<br />红的那一行按U<br />![image.png](p/F7.png)<br />这里C一下<br />E8多余的数据NOP掉<br />![image.png](p/F8.png)<br />![image.png](p/F9.png)<br />![image.png](p/F10.png)<br />这有个花指令，也修一下，和上面的原理一样<br />![image.png](p/F11.png)<br />P一下<br />![image.png](p/F12.png)<br />tab<br />![image.png](p/F13.png)<br />都修复完了记得`apply patch to`保存到`exe`文件里，不然动调的时候还是会有反调试<br />完整的main函数<br />![image.png](p/F14.png)<br />加载的资源数据最终储存在27行`dword_C8541C`里，dll资源在`sub_C81CE0`里加载，我们要把它取出来查看输出函数相关参数<br />进`sub_C81CE0`在结尾下个断点，我们需要`IpMem`里的资源<br />![image.png](p/F15.png)<br />动调点进去<br />![image.png](p/F16.png)<br />按D直到出现这个地址<br />![image.png](p/F17.png)<br />跟过去<br />![image.png](p/F18.png)<br />这里Shift+*直接OK<br />![image.png](p/F19.png)<br />Shift+E提取数据<br />![image.png](p/F20.png)<br />选择`raw bytes`格式，导出后缀无所谓<br />放进CFF查看<br />![image.png](p/F21.png)<br />这里看到了调用了`CryptEncrypt`函数
> CryptEncrypt 是 Windows 加密服务提供程序 (Cryptographic Service Provider, CSP) 中的一个函数，用于加密数据。它是基于 Windows 的加密API (Cryptography API: Next Generation, also known as CNG) 的一个组成部分。
> 在使用 CryptEncrypt 函数之前，通常需要先初始化一个 CryptoAPI_BLOB 结构体，然后使用 CryptCreateHashHandle 或其他相关函数来创建一个哈希句柄。之后，可以使用这个句柄和密钥来调用 CryptEncrypt 函数。

总之是Windows自带的加密函数<br />在main函数34行 加密部分往里面找，最后看到一个参数是0xB的，对应加密函数<br />![image.png](p/F22.png)<br />改成解密函数 对应0x5<br />![image.png](p/F23.png)<br />断在v6启动的位置<br />![image.png](p/F24.png)<br />发现动调到这里又自动退出了<br />![image.png](p/F25.png)<br />看一下汇编<br />![image.png](p/F26.png)
> JNZ指令的含义是“Jump if Not Zero”，即当结果不为0时转移。如果执行操作的寄存器或操作数的结果不为0，则控制权转移到指定的标签位置。
> JZ指令的含义是“Jump if Zero”，即当结果为0时转移。如果执行操作的寄存器或操作数的结果为0，则控制权转移到指定的标签位置。

所以这里把`jz`改成`jnz`就不会跳转到`exit`去了<br />（也可以动调的时候该标志位）<br />
## 注意动调之前一定要`apply patch to`！！！
继续动调，跑到这里又跳出去了<br />![image.png](p/F27.png)<br />注意一下第10行有关于文件路径的函数`SHGetKnownFolderPath`和参数`ppszPath`<br />第四行定义的`ppszPath`是一个指向宽字符串的指针，用于存储已知文件夹的路径。直接点开看不见，我们动调。<br />![image.png](p/F28.png)<br />点进去看一下<br />![image.png](p/F29.png)<br />一直D到字符串出来<br />![image.png](p/F30.png)<br />可以看到要求的路径是在你的桌面<br />回到原来的函数<br />![](p/F31.png)<br />看第12行 `if ( PathCombineW(pszDest, ppszPath, L"document") ):`使用`PathCombineW`函数将已知文件夹的路径和`"document"`字符串合并，存储在`pszDest`中。<br />那路径就是 `C:\\Users\37785\\Desktop\\document`（Desktop前的部分因人而异）<br />第13行`if ( PathCombineW(FileName, pszDest, L"*.*") ):`使用`PathCombineW`函数将`pszDest`和`"."`字符串合并，存储在FileName中。这将生成一个用于搜索所有文件的通配符模式。（文心一言友情供稿）<br />也不知道是怎么搜的，反正把`1.txt`扔进去应该没问题<br />动调F8到这里<br />![image.png](p/F32.png)<br />看一下`1.txt`<br />![image.png](p/F33.png)<br />OHHHHHHHHHHHHHHHH！！！！！！！！！！！！！！！！
