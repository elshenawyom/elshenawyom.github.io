---
layout: post
title: pdf troubleshoot
date: 2025-01-24 00:03:00
description: 
tags: 
categories: 
thumbnail: assets/img/blog/state-religion/timeline.jpg
images:
  compare: true
  slider: true
---

x
<embed 
    src="/assets/pdf/banachtarski_slides.pdf" 
    type="application/pdf" 
    width="100%" 
    height="600px">



xxx
<iframe 
    src="https://drive.google.com/viewerng/viewer?embedded=true&url=elshenawyom.github.io/assets/pdf/banachtarski_slides.pdf" 
    style="width: 100%; height: 600px;" 
    frameborder="0">
</iframe>



xxxx


%%% MY SOLUTION

<div class="outer-pdf" style="-webkit-overflow-scrolling: touch; overflow: auto;">
 <div class="pdf">
     <iframe class="desktop-pdf" scrolling="auto" 
        src="/assets/pdf/banachtarski_slides.pdf" 
        width="100%" height="90%" 
        type='application/pdf'>
     </iframe>
     <iframe class="mobile-pdf" scrolling="auto" 
        src="https://drive.google.com/viewerng/viewer?embedded=true&url=/assets/pdf/banachtarski_slides.pdf" 
        width="100%" height="90%" 
        type='application/pdf'>
     </iframe>
 </div>
</div>


%%% try again

<iframe id="theFrame" src="/assets/pdf/banachtarski_slides.pdf" style="height:1000px; width:100%;"></iframe>
<script>
document.getElementById("theFrame").contentWindow.onload = function() {
    this.document.getElementsByTagName("img")[0].style.width="100%";
};
</script>

