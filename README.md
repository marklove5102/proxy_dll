# Legacy Windows Kernel Work (2010–2022)

This repository has moved home.  
Originally under **@Fyyre**, it now lives in the Forge Constellation at [@noct-ml](https://github.com/noct-ml).

For over a decade I reverse-engineered the black boxes that run Windows — SSDT hooks, driver monitoring, PatchGuard bypasses, creative CRT unhooking for protected applications, and direct NTAPI trickery. Tools like DrvMon and Kernel Detective were battle-tested in the wild and stayed private until the right time.

That same instinct — to look inside the machine without fear — is exactly what now drives my AI forensics work.

The forge never changed. Only the medium did.

→ Current work lives in the Project Portfolio below.

*(Original README continues below — leave everything else untouched)*

---

*(Original README continues below — leave everything else untouched)*

# proxy

A simple system for hooking protected applications.
I have employed this method to make hooks for various
MMORPG games, for the past two years.

```C
void __security_init_cookie(void);	// dear friend!
```

## Details

Most Windows binaries have a predictable pattern that can be used to our advantage.

To do this we take advantage of the fact that **`__security_init_cookie`** is called by the CRT when it's initialized.
The initialization is the first call at the OEP:

```
__int64 start()
{
  __security_init_cookie();	// hello!
  return __scrt_common_main_seh();
}
```

**GetSystemTimeAsFileTime** is the first import called first in **`__security_init_cookie`**

GetSystemTimeAsFileTime is embarassingly easily to recreate within our own code.
We only need only a few structures. And I do not wish to share my ntdll.h =x

We recreate the original GetSystemTimeAsFileTime function, and perform our hooking inside,
having added a check to determine when our target is decrypted in memory, and unhooking our hook
as our last step, not to add lag to the game.

**CLIENT_ID**
**KSYSTEM_TIME**
**KUSER_SHARED_DATA** (however we only need the first 5 entries of this structure)

For further elaboration on this idea, I suggest you look over the project code,
which I have made compatible with Windows 11 22H2, ...

In hopes someone finds purpose with it,

-Fyyre
