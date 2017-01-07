---
layout: page
title: Contact Me
css: "/css/contact.css"
---

<form action="https://formspree.io/jonmcalder@gmail.com" method="POST" class="form" id="contact-form">
  <p>If you'd like to get in touch, please send me a message using the form 
  below. Alternatively, you can make use of the links included in the footer to 
  interact with me via social media.</p>
  <div class="row">
    <div class="col-xs-6">
      <input type="text" name="name" class="form-control input-lg" placeholder="Name" title="Name">
    </div>
    <div class="col-xs-6">
      <input type="email" name="_replyto" class="form-control input-lg" placeholder="Email" title="Email">
    </div>
  </div>
  <input type="hidden" name="_subject" value="New message from http://jonmcalder.github.io">
  <textarea type="text" name="content" class="form-control input-lg" placeholder="Message" title="Message" required="required" rows="3"></textarea>
  <input type="text" name="_gotcha" style="display:none">
  <input type="hidden" name="_next" value="//jonmcalder.github.io/thanks.html" />
  <button type="submit" class="btn btn-lg btn-primary">Send</button>
</form>