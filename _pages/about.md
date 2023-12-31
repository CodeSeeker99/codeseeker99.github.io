---
layout: page
title: 'Hi, I''m'
permalink: /about/
published: true
---


### Rishabh Bajpai

<div style="float: left">
    <span>
    <img src="/images/LocalGuide.jpg" alt="self pic" style="float: left;width:200px;height:200px;margin:10px">
    I'm a B.E. Computer Science graduate from BITS-Pilani, K. K. Birla Goa Campus with experience in artificial intelligence (AI), 
    biomedical signal processing and neuro-imaging research. I'm currently a lead software developer (SDE2) at Amazon within the 
    Last Mile Delivery Technology organization. My research and work experiences can be best summarized on my 
    <a target="_blank" rel="noopener noreferrer" href="https://scholar.google.com/citations?user={{ site.footer-links.scholar }}"> Google Scholar </a> and 
    <a target="_blank" rel="noopener noreferrer" href="https://www.linkedin.com/in/{{ site.footer-links.linkedin }}"> LinkedIn profile </a>
    </span>
</div>

While my current citations are limited to the field of deep learning and biomedical signal processing, in the future, 
I plan to focus my research in the field of artificial general intelligence (AGI) and neuroscience. I find the use of foundational models 
for medical imaging is very interesting and I'm looking to explore that as well. 

Apart from the aforementioned, I'm passionate about video editing. I've covered various fests at my college and lead creative 
video production for promotional purposes. I grew up watching and editing a lot of Anime Music Videos (AMVs). 
I'm an enthusiast gamer as well, hit me up on Valorant. Finally, I love playing Ultimate Frisbee. 
Proud alumni of the BITS-UFC club at college. Maybe you caught me at Ahmedabad Ultimate?

<!-- Slideshow container -->
<div class="slideshow-container">
  <!-- Full-width images with number and caption text -->
  <div class="mySlides fade">
    <div class="numbertext">1 / 2</div>
    <img src="/images/video.jpg" class="slidepic" alt="pic1">
    <div class="text">Covering college festivals</div>
  </div>

  <div class="mySlides fade">
    <div class="numbertext">2 / 2</div>
    <img src="/images/BITSUFC.png" class="slidepic" alt="pic2">
    <div class="text">Ultimate Frisbee Team</div>
  </div>

  <!-- Next and previous buttons -->
  <a class="prev" onclick="plusSlides(-1)">&#10094;</a>
  <a class="next" onclick="plusSlides(1)">&#10095;</a>
</div>

<!-- The dots/circles -->
<div style="text-align:center">
  <span class="dot" onclick="currentSlide(1)"></span>
  <span class="dot" onclick="currentSlide(2)"></span>
</div> 

I started this blog in my 3rd year of engineering as a place to write about anything related to my interests until Covid 
and some workload put a pause on it. Expect more to be posted as I enter into academia once again to pursue my research interests.

<script>
let slideIndex = 1;
showSlides(slideIndex);

// Next/previous controls
function plusSlides(n) {
  showSlides(slideIndex += n);
}

// Thumbnail image controls
function currentSlide(n) {
  showSlides(slideIndex = n);
}

function showSlides(n) {
  let i;
  let slides = document.getElementsByClassName("mySlides");
  let dots = document.getElementsByClassName("dot");
  if (n > slides.length) {slideIndex = 1}
  if (n < 1) {slideIndex = slides.length}
  for (i = 0; i < slides.length; i++) {
    slides[i].style.display = "none";
  }
  for (i = 0; i < dots.length; i++) {
    dots[i].className = dots[i].className.replace(" active", "");
  }
  slides[slideIndex-1].style.display = "block";
  dots[slideIndex-1].className += " active";
} 
</script>
