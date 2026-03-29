---
title: "Contact"
permalink: /contact/
layout: single
author_profile: true
---

Have a question, want to collaborate, or just want to say hello? Feel free to reach out using the form below or connect with me on [LinkedIn](https://linkedin.com/in/taulant-koka-36a7971b6) or [GitHub](https://github.com/taulantkoka).

---

<!-- 
  HOW TO SET UP THE CONTACT FORM:
  1. Go to https://formspree.io and create a free account
  2. Create a new form and copy your form ID (looks like: xabcdefg)
  3. Replace YOUR_FORM_ID below with your actual Formspree ID
-->

<form action="https://formspree.io/f/YOUR_FORM_ID" method="POST" style="max-width: 600px;">
  <p>
    <label for="name"><strong>Name</strong></label><br/>
    <input type="text" name="name" id="name" required
      style="width: 100%; padding: 0.5em; border: 1px solid #ccc; border-radius: 4px; background: transparent; color: inherit;">
  </p>
  <p>
    <label for="email"><strong>Email</strong></label><br/>
    <input type="email" name="_replyto" id="email" required
      style="width: 100%; padding: 0.5em; border: 1px solid #ccc; border-radius: 4px; background: transparent; color: inherit;">
  </p>
  <p>
    <label for="message"><strong>Message</strong></label><br/>
    <textarea name="message" id="message" rows="6" required
      style="width: 100%; padding: 0.5em; border: 1px solid #ccc; border-radius: 4px; background: transparent; color: inherit;"></textarea>
  </p>
  <p>
    <input type="hidden" name="_subject" value="New message from taulantkoka.com">
    <button type="submit" class="btn btn--primary">Send Message</button>
  </p>
</form>
