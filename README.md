Modify Microsoft Basic Display Driver to get a better resolution
=================================

Microsoft has introduced a new out-of-box driver, Microsoft Basic Display Adapter Driver. This driver is used during installation, a suitable driver is not found(always old adapters no longer supported by manufacturers), safe mode. And you can always use it when your graphics adapter has a buggy driver.
However, as its name, basic display adapter, it provides 640*480 when boot in headless mode, that is, no monitor is attached to a systtem. And actually, we usually put our servers, HTPC(it become headless if you switch to other source in your TV) in this mode.
By modifying the default value in BasicDisplay.sys, we can change this. After a bit googling, 1366*736 is most used resolution, and 1600*900/1920*1080 is used in recent HD monitors.

use IDA, according the function names, we can easily reach here:
PAGE:000000000001852C ; private: void BASIC_DISPLAY_DRIVER::GoToHeadless(enum _BDD_HEADLESS_REASON)
PAGE:000000000001852C ?GoToHeadless@BASIC_DISPLAY_DRIVER@@AEAAXW4_BDD_HEADLESS_REASON@@@Z proc near
PAGE:000000000001852C ; CODE XREF: BASIC_DISPLAY_DRIVER::FallbackStart(_DXGK_DISPLAY_INFORMATION_PLUS_EDID *,uchar)+141p
PAGE:000000000001852C ; BASIC_DISPLAY_DRIVER::FallbackStart(_DXGK_DISPLAY_INFORMATION_PLUS_EDID *,uchar)+310p �
PAGE:000000000001852C
PAGE:000000000001852C arg_0 = qword ptr 8
PAGE:000000000001852C
PAGE:000000000001852C mov [rsp+arg_0], rbx
PAGE:0000000000018531 push rdi
PAGE:0000000000018532 sub rsp, 20h
PAGE:0000000000018536 movsxd rbx, edx
PAGE:0000000000018539 mov rdi, rcx
PAGE:000000000001853C call cs:__imp_WdLogNewEntry5_WdEvent
PAGE:0000000000018542 movsxd r8, dword ptr [rdi+238h]
PAGE:0000000000018549 mov rcx, rax
PAGE:000000000001854C mov [rax+18h], r8
PAGE:0000000000018550 mov [rax+20h], rbx
PAGE:0000000000018554 call cs:__imp_WdLogEvent5_WdEvent
PAGE:000000000001855A bts dword ptr [rdi+29Ch], 0Ch
PAGE:0000000000018562 mov [rdi+238h], ebx
PAGE:0000000000018568 or dword ptr [rdi+1D0h], 0FFFFFFFFh
PAGE:000000000001856F mov rbx, [rsp+28h+arg_0]
PAGE:0000000000018574 xor eax, eax
PAGE:0000000000018576 or dword ptr [rdi+1DCh], 8
PAGE:000000000001857D mov [rdi+1F0h], rax
PAGE:0000000000018584 mov [rdi+1D8h], eax
PAGE:000000000001858A mov dword ptr [rdi+1B8h], 280h
PAGE:0000000000018594 mov dword ptr [rdi+1BCh], 1E0h
PAGE:000000000001859E mov qword ptr [rdi+1C0h], 0A00h
PAGE:00000000000185A9 add rsp, 20h
PAGE:00000000000185AD pop rdi
PAGE:00000000000185AE retn
PAGE:00000000000185AE ?GoToHeadless@BASIC_DISPLAY_DRIVER@@AEAAXW4_BDD_HEADLESS_REASON@@@Z endp
PAGE:00000000000185AE

modify 280h to width,1e0h to height.

For your convenience, I have patched x64/x86, and there are 3 resolutions mod in compressed files.

1. Download this package

2. Change your current adapter driver from a buggy driver, e.g. Intel driver. skip this if you are using Basic Adapter Driver already. reboot, and you should get a very low resolution at 640*480( if you are remoting). It�s best to do a check in device manager, it�s using basic adapter driver.

3. Take a file you wanted from the compressed file, go to C:\Windows\System32\Drivers, modify the files security, take the ownership from TrustInstaller to Administrators, and assign Administrators to Full Access. replace the file, reboot. now you can get the respective resolution according what files you used.

as to search friendly:

Windows 8 enable widescreen on basic display driver.
Windows 8.1 enable widescreen on basic display driver.
Windows 8/8.1 enble native resolution with old video adapters.
D54250WYK �headless� issue
D34010WYK �headless� issue
Intel display driver headless issue
Intel NUC black screen