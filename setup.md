---
layout: page
title: Setup
root: .
---

To follow this workshop, you will need three things: an account on the Supercomputing Wales facility,
an SSH client, and the Filezilla application for transferring files.

{% comment %} {% endcomment %}
<div id="scw">
  <h3>Supercomputing Wales</h3>

  In this workshop we will use the Supercomputing Wales facilities to
  learn to use High-Performance Computing. For this, you will need an
  account on the Supercomputing Wales facilities.

  <ol>
    <li>Visit <a href="https://my.supercomputing.wales/">My
    Supercomputing Wales</a></li>
	<li>Sign in with your Swansea University email and password</li>
	<li>Fill in the form requesting a Supercomputing Wales
    account. Your aaccount request will be processed by an
    administrator.</li>
	<li>Once you receive an email indicating that your account has been
    created, then revisit <a
    href="https://my.supercomputing.wales/">My Supercomputing
    Wales</a>, and log in again if necessary.</li>
	<li>Click the "Reset SCW Password" button, and enter a password
    that you will use to access the Supercomputing Wales
    hardware. (This does not have to be the same as your Swansea
    University password.) Click Submit.</li>
	<li>Under "Join a project", enter {{page.scw_project}} as the
    project code for this training session, and click "Join".</li>
  </ol>
</div> {% comment %} End of 'Supercomputing Wales' section. {% endcomment %}

{% comment %} {% endcomment %}
<div id="SSH">
  <h3>SSH</h3>
  
  SSH is used to connect to the Unix shell on machines across the network.

  <div class="row">
    <div class="col-md-4">
      <h4 id="ssh-windows">Windows</h4>
      <ol>
        <li> If you are using Windows and also following the [Unix Shell](https://swcarpentry.github.io/shell-novice) lesson,
then the Git Bash tool installed as part of that lesson will provide you with this.</li>
        <li> Otherwise, then download and install PuTTY from [https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html](https://www.chiark.greenend.org.uk/~sgtatham/putty/latest.html).</li>
      </ol>
    </div>
    <div class="col-md-4">
      <h4 id="ssh-mac">macOS</h4>
      <ol>
        <li>SSH is installed as part of macOS and is available via the Terminal application.</li>
      </ol>
    </div>
    <div class="col-md-4">
      <h4 id="ssh-linux">Linux</h4>
      <ol>
        <li>SSH is installed as part of Linux and is available through a terminal/console application.</li>
      </ol>
    </div>
  </div>
</div>
{% comment %} End of 'SSH' section {% endcomment %}


{% comment %} {% endcomment %}
<div id="filezilla">
  <h3>FileZilla</h3>

  We will use FileZilla to transfer files to and from the Supercomputing Wales facilities.

  <div class="row">
    <div class="col-md-4">
      <h4 id="filezilla-windows">Windows</h4>
      <ol>
        <li>Open <a href="https://filezilla-project.org/download.php?platform=win64">https://filezilla-project.org/download.php?platform=win64</a> with your web browser.</li>
	<li>Download and run the installer. You only need FileZilla, not FileZilla Pro.</li>
	<li>Follow the on-screen instructions. Note that while the installer may try to convince you to install additional software, you do not need to agree to this; if you do not agree to the additional license agreement, FileZilla will still install.</li>
      </ol>
    </div>
    <div class="col-md-4">
      <h4 id="filezilla-mac">macOS</h4>
      <ol>
        <li>Open <a href="https://filezilla-project.org/download.php?platform=osx">https://filezilla-project.org/download.php?platform=osx</a> with your web browser.</li>
	<li>Download and open the Client bundle. You only need FileZilla, not FileZilla Pro.</li>
	<li>Copy the FileZilla app to your Applications folder.</li>
      </ol>
    </div>
    <div class="col-md-4">
      <h4 id="filezilla-linux">Linux</h4>
      <ol>
        <li>Search for and install FileZilla in your distribution's package manager.</li>
      </ol>
    </div>
  </div>
</div>
{% comment %} End of 'FileZilla' section {% endcomment %}
