---
layout: page
icon: fas fa-envelope
order: 6
---


<p>Feel free to drop me a message using the form below:</p>

<form id="my-form" action="https://formspree.io/f/mblyqqok" method="POST">
  <label for="email"><strong>Your Email</strong></label><br>
  <input type="email" id="email" name="email" required style="width: 100%; max-width: 400px;"><br><br>

  <label for="message"><strong>Your Message</strong></label><br>
  <textarea id="message" name="message" rows="6" required style="width: 100%; max-width: 400px;"></textarea><br><br>

  <button id="my-form-button" type="submit">Submit</button><br><br>

  <p id="my-form-status"></p>
</form>

<script>
  var form = document.getElementById("my-form");

  async function handleSubmit(event) {
    event.preventDefault();
    var status = document.getElementById("my-form-status");
    var data = new FormData(event.target);
    fetch(event.target.action, {
      method: form.method,
      body: data,
      headers: {
        'Accept': 'application/json'
      }
    }).then(response => {
      if (response.ok) {
        status.innerHTML = "<span style='color: green;'>Thanks for your submission!</span>";
        form.reset();
      } else {
        response.json().then(data => {
          if (Object.hasOwn(data, 'errors')) {
            status.innerHTML = "<span style='color: red;'>" + data["errors"].map(error => error["message"]).join(", ") + "</span>";
          } else {
            status.innerHTML = "<span style='color: red;'>Oops! There was a problem submitting your form</span>";
          }
        });
      }
    }).catch(error => {
      status.innerHTML = "<span style='color: red;'>Oops! There was a problem submitting your form</span>";
    });
  }

  form.addEventListener("submit", handleSubmit);
</script>