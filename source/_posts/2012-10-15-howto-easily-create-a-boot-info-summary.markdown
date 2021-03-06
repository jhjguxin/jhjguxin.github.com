---
layout: post
title: "HOWTO : easily create a Boot-Info summary"
date: 2012-05-25 20:16
comments: true
categories: [ubuntu]
tags: [Boot-Repair]
---
## HOWTO : easily create a Boot-Info summary
<strong>Boot-Repair</strong> is a simple tool to repair frequent boot issues you may encounter in Ubuntu like when you can't boot Ubuntu after installing Windows or another Linux distribution, or when you can't boot Windows after installing Ubuntu, or when GRUB is not displayed anymore, some upgrade breaks GRUB, etc.

Boot-Repair lets you fix these issues with a simple click, which (generally reinstalls GRUB and) restores access to the operating systems you had installed before the issue.

Boot-Repair also has advanced options to backup table partitions, backup bootsectors, create a Boot-Info-Summary (to get help by email or forum), or change the default repair parameters: configure GRUB, add kernel options (acpi=off ...), purge GRUB, change the default OS, restore a Windows-compatible MBR, repair a broken filesystem, specify the disk where GRUB should be installed, etc.

Boot-Repair is a free software, licensed under GNU-GPL. Boot-Repair should be <a href="https://bugs.launchpad.net/boot-repair/+bug/806291">soon</a> included in Ubuntu official repositories, until then use it at your own risks.

<img title="http://pix.toile-libre.org/upload/original/1335260967.png" src="http://pix.toile-libre.org/upload/original/1335260967.png" alt="http://pix.toile-libre.org/upload/original/1335260967.png" />

&nbsp;
<h1 id="Getting_Boot-Repair">Getting Boot-Repair</h1>
&nbsp;
<h2 id="A1st_option_:_get_a_CD_including_Boot-Repair">1st option : get a CD including Boot-Repair</h2>
The easiest way to use Boot-Repair is to burn one of the following disks and boot on it.
<ul>
	<li><a href="https://sourceforge.net/projects/boot-repair-cd/files/">Boot-Repair-Disk</a> is a CD starting Boot-Repair automatically. (English only, 32&amp;64bits compatible, based on Debian-live so Wifi drivers are not recent).</li>
	<li>Boot-Repair is also included in <a href="https://help.ubuntu.com/community/UbuntuSecureRemix">Ubuntu-Secure-Remix</a> (multi-languages, ok for Wifi, based on Ubuntu 12.04 LTS, run Boot-Repair from the Dash)</li>
</ul>
Remark : you can also install the ISO on a <a href="https://help.ubuntu.com/community/Installation/FromUSBStick">live-USB</a> (eg via <a href="http://unetbootin.sourceforge.net/">UnetBootin</a>, <a href="http://www.linuxliveusb.com/">LiliUSB</a> or <a href="https://help.ubuntu.com/community/MultiSystem">MultiSystem</a>).

&nbsp;
<h2 id="A2nd_option_:_install_Boot-Repair_in_Ubuntu">2nd option : install Boot-Repair in Ubuntu</h2>
Remark: this can also be performed from a live-CD or live-USB.

Either add ‘ppa:yannubuntu/boot-repair’ to your Software Sources via the Software Centre or, for speeds-sake, add it using a new Terminal session:

&nbsp;
<pre>sudo add-apt-repository ppa:yannubuntu/boot-repair &amp;&amp; sudo apt-get update
sudo apt-get install -y boot-repair &amp;&amp; boot-repair</pre>
Boot-Repair can be installed &amp; used from any Ubuntu session (normal session, or live-CD, or live-USB). PPA packages are available for Ubuntu 10.04, 10.10, 11.04, 11.10 and 12.04.

&nbsp;
<h2>Adding this PPA to your system</h2>
<p id="yui_3_2_0_1_1337949503297112">You can update your system with unsupported packages from this untrusted PPA by adding <strong>ppa:yannubuntu/boot-repair</strong> to your system's Software Sources. (<a href="https://launchpad.net/+help-soyuz/ppa-sources-list.html" target="help">Read about installing</a>)</p>

<div><a href="https://launchpad.net/%7Eyannubuntu/+archive/boot-repair/+index#">Technical details about this PPA</a></div>
This PPA can be added to your system manually by copying the lines below and adding them to your system's software sources.
<div id="series-widget-div"><form id="yui_3_2_0_1_1337949503297108">Display sources.list entries for:</form></div>
<pre id="sources-list-entries">deb <a href="http://ppa.launchpad.net/yannubuntu/boot-repair/ubuntu">http://ppa.launchpad.net/yannubuntu/boot-repair/ubuntu</a> precise main
deb-src <a href="http://ppa.launchpad.net/yannubuntu/boot-repair/ubuntu">http://ppa.launchpad.net/yannubuntu/boot-repair/ubuntu</a> precise main</pre>
<dl id="signing-key"><dt>Signing key:</dt><dd><a href="http://keyserver.ubuntu.com:11371/pks/lookup?search=0x3C48D16124B50277AF10D27F32B18A1260D8DA0B&amp;op=index"> <code>1024R/60D8DA0B</code> </a> (<a href="https://launchpad.net/+help-soyuz/ppa-sources-list.html" target="help">What is this?</a>)</dd></dl>
<h1 id="Using_Boot-Repair">Using Boot-Repair</h1>
&nbsp;
<h2 id="Recommended_repair">Recommended repair</h2>
<ul>
	<li>launch Boot-Repair from either :
<ul>
	<li>the dash (Unity)</li>
	<li>System-&gt;Administration-&gt;Boot-Repair menu (Gnome)</li>
	<li>by typing 'boot-repair' in a terminal</li>
</ul>
</li>
	<li>Then try "Recommended repair" button. When repair is finished, note the URL that appeared on a paper, then reboot and check if you recovered access to your OSs.</li>
</ul>
If the repair did not succeed, indicate the URL to people who help you by email or forum.

&nbsp;
<h2 id="Advanced_options">Advanced options</h2>
<img title="http://pix.toile-libre.org/upload/img/1335263156.png" src="http://pix.toile-libre.org/upload/img/1335263156.png" alt="http://pix.toile-libre.org/upload/img/1335263156.png" /> <img title="http://pix.toile-libre.org/upload/img/1335263804.png" src="http://pix.toile-libre.org/upload/img/1335263804.png" alt="http://pix.toile-libre.org/upload/img/1335263804.png" /> <img title="http://pix.toile-libre.org/upload/img/1335263271.png" src="http://pix.toile-libre.org/upload/img/1335263271.png" alt="http://pix.toile-libre.org/upload/img/1335263271.png" /> <img title="http://pix.toile-libre.org/upload/img/1335263417.png" src="http://pix.toile-libre.org/upload/img/1335263417.png" alt="http://pix.toile-libre.org/upload/img/1335263417.png" /> <img title="http://pix.toile-libre.org/upload/img/1335263366.png" src="http://pix.toile-libre.org/upload/img/1335263366.png" alt="http://pix.toile-libre.org/upload/img/1335263366.png" />

&nbsp;
<h1 id="External_Links">External Links</h1>
<ul>
	<li><a href="https://sourceforge.net/p/boot-repair/">Official website of Boot-Repair</a></li>
	<li><a href="http://ubuntuforums.org/showthread.php?p=10871917#post10871917">Topic "Boot-repair: Graphical tool to repair the PC boot in 1 clic !"</a> - on Ubuntu forum, for any questions/comments.</li>
	<li><a href="http://ubuntuforums.org/showthread.php?t=1821980">HOWTO : easily create a Boot-Info summary with Boot-Repair</a></li>
</ul>
