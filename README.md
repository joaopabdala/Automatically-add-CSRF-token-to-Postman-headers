# Automatically Add CSRF Token to Postman Headers

The "CSRF token mismatch" error occurs when you're trying to test a Laravel API due to Laravel's CSRF protection. You can read more about its usage in the [Laravel Documentation](https://laravel.com/docs/11.x/csrf). Here's a quick script that you can use to automatically set the token in your collection headers so you won't have to worry about it anymore.

**Script:**

```javascript
pm.sendRequest({
    url: 'http://localhost/', // Adjust the URL as needed
    method: 'GET'
}, function (error, response) {
    if (error) {
        console.log('Request failed:', error);
        return;
    }

    // Retrieve all 'Set-Cookie' headers, as sometimes there is more than one, like the 'laravel_session' cookie
    let setCookieHeaders = response.headers.all();
    let csrfToken = null;

    // Loop through all headers to find 'XSRF-TOKEN'
    setCookieHeaders.forEach(header => {
        if (header.key === 'Set-Cookie') {
            let cookies = header.value.split(';');
            cookies.forEach(cookie => {
                if (cookie.trim().startsWith('XSRF-TOKEN=')) {
                    csrfToken = decodeURIComponent(cookie.split('=')[1]);
                }
            });
        }
    });

    // Automatically set the CSRF token in your headers
    pm.request.headers.add({ key: "X-XSRF-TOKEN", value: csrfToken });
});
```

**How to Add It:**

1. Click on your Postman Collection, go to **Scripts**, and select **Pre-request**.
2. Copy and paste the script.

![image](https://github.com/user-attachments/assets/df30e47f-090c-4108-9951-7660fd6c8434)


