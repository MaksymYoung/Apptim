# Frontend Performance Testing - Apptim Task | Mentoring

## About the performance of the Wikipedia application based on Apptim results (Wikipedia build 50608)

I ran a 37-second search-and-read scenario on an Android 13 emulator with Apptim's default thresholds, mainly to get a baseline for build 50608.

The app itself looks stable. There were no crashes, memory peaked at 125 MB after growing early in the run and then flattening out, and network use was modest at 2.8 MB down and 0.0 MB up. Average app CPU was 57.7%, with a peak of 80%. The device-level numbers (386% CPU, 2.4 GB memory) describe the whole emulator rather than Wikipedia: the emulator has four cores, so a figure near 400% means almost all of them were busy, and Apptim adds CPU up across cores.

The red flags are all rendering: an average FPS of 6, 326 janks, a worst layout time of 87.9 ms against a 16.67 ms frame budget, and animations up to 27.9 ms. The energy score (489) and average device memory (2408 MB) are only moderate, and the energy score is an estimate built from CPU and GPU activity, so it largely follows the rendering. Together with the integrated AMD GPU, the big 1440 by 3040 screen and a debuggable build, I'd treat the smoothness numbers as mostly an emulator limitation that still needs confirming on a real device. Database queries are fast.

## Customize the threshold settings

For the same build 50608 I replaced Apptim's defaults with limits based on common sense rather than on this run: FPS below 30 for Moderate and below 15 for Warning, janks above 100 and 200, layout time above 16.7 and 33.3 ms, average app CPU above 40% and 60%, peak app memory above 120 and 150 MB, network download above 1.5 and 3 MB, slow database queries above 16 and 50 ms, and any crash as a Warning.

The same app suddenly looks worse in the report: 5 warnings and 7 moderates, against 4 warnings and 2 moderates with the defaults. That is mostly the thresholds talking and not the app, because the numbers barely moved between the two runs. FPS is 6 in both, average CPU is 58.1% against 57.7%, peak memory is 128.4 MB against 125.0 MB, layout time is 94.6 ms against 87.9 ms, and the energy score is 479 against 489. CPU and memory, which looked fine by default, now show as Moderate simply because the limits are stricter.

Two numbers did differ. Network download was 1.9 MB against 2.8 MB, and one database query took 94 ms (the database was empty on the first run) against 1 ms in the baseline. It only shows up as a Warning because I set the limit at 50 ms; with Apptim's default of 100 ms it would have passed.

The takeaway is that thresholds change the verdict and not the performance. They should come from requirements and not from one run.

## About the performance of the Wikipedia application based on Apptim results (Wikipedia build 50606)

I ran a 49-second search-and-read scenario on the same emulator with Apptim's default thresholds, this time against build 50606, to get a reference for comparison with the newer build.

The app looks stable here too. There were no crashes, memory peaked at 127 MB, and the network footprint was small at 1.5 MB down and 0.1 MB up. Average app CPU was 52.7%, with a peak of 85.9%. The device-level numbers (400% CPU, 2.4 GB memory) again describe the emulator, not the app. The app size is 82.5 MB, above Apptim's default limit of 52 MB. Both builds report Debuggable: Yes.

Rendering is again where it goes wrong, and in a few places it is worse than in 50608: average FPS of 6, 436 janks, a worst layout time of 185 ms, animations up to 53.8 ms, and an energy score of 500. They are one problem seen from several angles, since slow frames lead to janks and low FPS, and the energy score follows the CPU and GPU load. Database queries are fast, with the slowest at 15 ms.

## Comparison of the two builds

Comparing the default-threshold runs, the two builds behave very much alike. Both are stable with no crashes, and both run at about 6 FPS, so the smoothness is the same on this emulator. Janks fall from 436 to 326, but the runs lasted 49 and 37 seconds and janks accumulate, so per second it is about 8.9 against 8.8, which is the same. CPU and energy are also close: average CPU is 57.7% on 50608 against 52.7% on 50606, peak CPU is 80% against 85.9%, and the energy score is 489 against 500. Memory is nearly identical too, at 125.0 against 127.2 MB at peak and about 99 against 103 MB on average.

The differences are in the peaks and in size. In 50608 the worst layout pass is 88 ms against 185 ms, the worst animation is 28 ms against 54 ms. The app itself is also much smaller, 38 MB against 82.5 MB. 50608 downloaded more, 2.8 MB against 1.5 MB, which may come from cache state or from how long I scrolled rather than from the build.

The customized run of 50608 backs this up, because it landed close to its own baseline (layout 94.6 ms, energy 479, FPS 6). Even so, this is one or two runs per build on an emulator, so I'd read the layout and animation gaps as an indication and not a precise result. Repeating each run a few times, and checking the rendering on a real device, would settle it.
