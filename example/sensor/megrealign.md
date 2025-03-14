---
title: Interpolating data from the CTF151 to the CTF275 sensor array using megrealign
category: example
tags: [meg, ctf]
redirect_from:
    - /example/megrealign/
---

# Interpolating data from the CTF151 to the CTF275 sensor array using megrealign

    % the two example files with the 151 and 275 channel CTF specifications are available from
    % https://download.fieldtriptoolbox.org/example/megrealign/

    grad151 = ft_read_sens('ctf151.mat');
    grad275 = ft_read_sens('ctf275.mat');

    vol = [];
    vol.r = 12;
    vol.o = [0 0 4];

    % in this example the dipole is carefully positioned on the same depth as
    % where the dipole layer for the interpolation will be located. The center
    % of the head is at [0 0 4], the radius is 12 cm, and the dipole layer that is
    % specified in ft_megrealign is 2.5 cm shifted inward from the head surface
    cfg = [];
    cfg.dip.pos = [0 0 13.5];  % 4 + 12 - 2.5
    cfg.dip.frequency = 1;
    cfg.headmodel = vol;
    cfg.grad = grad151;
    data151 = ft_dipolesimulation(cfg);
    cfg.grad = grad275;
    data275 = ft_dipolesimulation(cfg);

    cfg = [];
    avg151 = ft_timelockanalysis(cfg, data151);
    avg275 = ft_timelockanalysis(cfg, data275);

    % here I'll not only realign the 151 channel MEG data to the 275 channel and
    % vice versa, but also apply the ft_megrealign function from 151 to 151. In the
    % realignment the signal will be slightly distorted as a comparison of avg151_151
    % to avg151 would show.
    cfg = [];
    cfg.inwardshift = 3;
    cfg.headmodel = vol;
    cfg.template{1} = grad151; avg151_151 = ft_timelockanalysis([], ft_megrealign(cfg, avg151));
    cfg.template{1} = grad275; avg151_275 = ft_timelockanalysis([], ft_megrealign(cfg, avg151));
    cfg.template{1} = grad151; avg275_151 = ft_timelockanalysis([], ft_megrealign(cfg, avg275));
    cfg.template{1} = grad275; avg275_275 = ft_timelockanalysis([], ft_megrealign(cfg, avg275));

    % plot the realigned datasets
    cfg = [];
    figure; ft_multiplotER(cfg, avg151_151);
    title('Interpolated from 151 to 151');
    orient portrait; print -dpng -r300 avg151_151.png

{% include image src="/assets/img/example/megrealign/avg151_151.png" width="500" %}

    figure; ft_multiplotER(cfg, avg151_275);
    title('Interpolated from 151 to 151');
    orient portrait; print -dpng -r300 avg151_275.png

{% include image src="/assets/img/example/megrealign/avg151_275.png" width="500" %}

    figure; ft_multiplotER(cfg, avg275_151);
    title('Interpolated from 151 to 151');
    orient portrait; print -dpng -r300 avg275_151.png

{% include image src="/assets/img/example/megrealign/avg275_151.png" width="500" %}

    figure; ft_multiplotER(cfg, avg275_275);
    title('Interpolated from 151 to 151');
    orient portrait; print -dpng -r300 avg275_275.png

{% include image src="/assets/img/example/megrealign/avg275_275.png" width="500" %}

    % now plot them together
    cfg = [];
    figure; ft_multiplotER(cfg, avg151, avg151_151, avg275_151);
    orient portrait; print -dpng -r300 compare151.png

{% include image src="/assets/img/example/megrealign/compare151.png" width="500" %}

    figure; ft_multiplotER(cfg, avg275, avg275_275, avg151_275);
    orient portrait; print -dpng -r300 compare275.png

{% include image src="/assets/img/example/megrealign/compare275.png" width="500" %}
