Seeing "Binder driver could not be opened.  Terminating." despite `binder,hwbinder,vndbinder` being enabled? Make sure CONFIG_ANDROID_BINDERFS is not enabled since otherwise it'll prevent binder devices initialization on binder_init() as seen below:
```
if (!IS_ENABLED(CONFIG_ANDROID_BINDERFS) &&
    strcmp(binder_devices_param, "") != 0) {
	/*
	* Copy the module_parameter string, because we don't want to
	* tokenize it in-place.
	 */
	device_names = kstrdup(binder_devices_param, GFP_KERNEL);
	if (!device_names) {
		ret = -ENOMEM;
		goto err_alloc_device_names_failed;
	}

	device_tmp = device_names;
	while ((device_name = strsep(&device_tmp, ","))) {
		ret = init_binder_device(device_name);
		if (ret)
			goto err_init_binder_device_failed;
	}
}
  ```
------

swrast gallium driver doesn't seem to expose EGL (at least on ancient bundled mesa 17), so most likely one needs proper GPU support.

Things get hilarious when one tries to force the usage of cpu rendering: Surfaceflinger tries really hard to find drm/msm libs in /vendor once it notices MDSS is available in kernel, and if you try to disable it - you have no drm framebuffers (yikes).

TL;DR: get your GPU up

----

Android-mainline has NO skip_initramfs, you can do funky stuff \o/
