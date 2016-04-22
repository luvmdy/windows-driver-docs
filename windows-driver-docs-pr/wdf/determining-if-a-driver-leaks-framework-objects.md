---
title: Determining If a Driver Leaks Framework Objects
author: windows-driver-content
description: This topic describes how you can find driver memory leaks caused by unreleased references. It applies to User-Mode Driver Framework (UMDF) version 1 and 2 drivers.
ms.assetid: 617cc678-e0db-4d2f-9d19-34b6cedad234
keywords: ["debugging scenarios WDK UMDF , determining whether a driver leaks framework objects", "UMDF WDK , debugging scenarios, determining whether a driver leaks framework objects", "UMDF WDK , determining whether a driver leaks framework objects"]
---

# Determining If a Driver Leaks Framework Objects


This topic describes how you can find driver memory leaks caused by unreleased references. It applies to User-Mode Driver Framework (UMDF) version 1 and 2 drivers.

## UMDF 1


In UMDF version 1, a call stack can cause a memory leak if each call to **AddRef** does not have a matching **Release** call.

To test if your UMDF version 1 driver leaks framework objects, use the following steps:

1.  Use the [WDF Verifier control application](https://msdn.microsoft.com/library/windows/hardware/ff556129) to set the verifier options that you want. During regular testing, start by setting **TrackObjects** and not **TrackRefCounts**.

    When the driver is unloaded, the framework's code verifier enters the debugger if a framework object was not deleted, and it prompts you to use the [**!wudfdumpobjects**](using-umdf-debugger-extensions.md) debugger extension. This debugger extension displays a list of undeleted objects.

2.  If the code verifier indicates that the driver is leaking framework objects, then use the control application to set the [**TrackRefCounts**](using-umdf-verifier.md) option.

    If this option is set, the verifier keeps track of references to framework objects while the driver runs. You can use the [**!wudfrefhist**](using-umdf-debugger-extensions.md) debugger extension to display each call stack (set of function calls) that increments or decrements an object's reference count. By examining the **AddRef** and **Release** calls in these call stacks, you should be able to find a stack that does not decrement the object's reference count and thus causes the leak.

For information about additional verifier options, see [Using UMDF Verifier](using-umdf-verifier.md).

For information about when to delete framework objects, see [Managing the Lifetime of Objects](managing-the-lifetime-of-objects.md).

## UMDF 2


In UMDF version 2, unreleased references are rare, but can occur due to call mismatches when using [**WdfObjectReference**](https://msdn.microsoft.com/library/windows/hardware/ff548758) and [**WdfObjectDereference**](https://msdn.microsoft.com/library/windows/hardware/ff548739).

To test if your UMDF version 2 driver leaks framework objects, use the following procedure:

1.  Follow the steps outlined in [Best Practices](enabling-a-debugger.md#bp) to configure your computer for UMDF debugging.
2.  To use tag tracking, enable both the UMDF Verifier and handle tracking in the registry. Both of these settings are stored in the driver's **Parameters\\Wdf** subkey of the **HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows NT\\CurrentVersion\\WUDF\\Services\\&lt;driver name&gt;** key.

    To enable the UMDF Verifier, set a nonzero value for **VerifierOn.**

    To enable handle tracking, set the value of **TrackHandles** to the name of one or more object types, or specify an asterisk (\*) to track all object types.

    You can also modify UMDF Verifier settings by using the [WdfVerifier.exe](https://msdn.microsoft.com/library/windows/hardware/ff556129) application.

3.  Reboot, establish a debugger connection, and then use the following debugger commands:

    -   [**!wdfkd.wdfdriverinfo 0x10**](https://msdn.microsoft.com/library/windows/hardware/ff565724) to display the handle hierarchy
    -   [**!wdfkd.wdftagtracker**](https://msdn.microsoft.com/library/windows/hardware/ff566126) to display tag information

If UMDF Verifier is on, memory leaks are detected at driver unload, just as in KMDF.

For additional information about using reference counts in KMDF and UMDF version 2 drivers, see [Framework Object Life Cycle](framework-object-life-cycle.md).

 

 

[Send comments about this topic to Microsoft](mailto:wsddocfb@microsoft.com?subject=Documentation%20feedback%20%5Bwdf\wdf%5D:%20Determining%20If%20a%20Driver%20Leaks%20Framework%20Objects%20%20RELEASE:%20%284/5/2016%29&body=%0A%0APRIVACY%20STATEMENT%0A%0AWe%20use%20your%20feedback%20to%20improve%20the%20documentation.%20We%20don't%20use%20your%20email%20address%20for%20any%20other%20purpose,%20and%20we'll%20remove%20your%20email%20address%20from%20our%20system%20after%20the%20issue%20that%20you're%20reporting%20is%20fixed.%20While%20we're%20working%20to%20fix%20this%20issue,%20we%20might%20send%20you%20an%20email%20message%20to%20ask%20for%20more%20info.%20Later,%20we%20might%20also%20send%20you%20an%20email%20message%20to%20let%20you%20know%20that%20we've%20addressed%20your%20feedback.%0A%0AFor%20more%20info%20about%20Microsoft's%20privacy%20policy,%20see%20http://privacy.microsoft.com/default.aspx. "Send comments about this topic to Microsoft")



