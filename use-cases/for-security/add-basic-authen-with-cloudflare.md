# Add basic Authen with Cloudflare

If you have one site html but you want protect this site with user and password, you can use worker Cloudflare for protect it

Example worker below:

```


/**
 * Shows how to restrict access using the HTTP Basic schema.
 * @see https://developer.mozilla.org/en-US/docs/Web/HTTP/Authentication
 * @see https://tools.ietf.org/html/rfc7617
 *
 * A user-id containing a colon (":") character is invalid, as the
 * first colon in a user-pass string separates user and password.
 */
const BASIC_USER = 'randomusername';
const BASIC_PASS = 'randompass';


addEventListener('fetch', event => {
  event.respondWith(
    handleRequest(event.request).catch(err => {
      const message = err.reason || err.stack || 'Unknown Error';

      return new Response(message, {
        status: err.status || 500,
        statusText: err.statusText || null,
        headers: {
          'Content-Type': 'text/plain;charset=UTF-8',
          // Disables caching by default.
          'Cache-Control': 'no-store',
          // Returns the "Content-Length" header for HTTP HEAD requests.
          'Content-Length': message.length,
        },
      });
    })
  );
});


/**
 * Receives a HTTP request and replies with a response.
 * @param {Request} request
 * @returns {Promise<Response>}
 */
async function handleRequest(request) {
  const { protocol, pathname } = new URL(request.url);

  // In the case of a Basic authentication, the exchange
  // MUST happen over an HTTPS (TLS) connection to be secure.
  if ('https:' !== protocol || 'https' !== request.headers.get('x-forwarded-proto')) {
    throw new BadRequestException('Please use a HTTPS connection.');
  }

  switch (pathname) {
    
    // case '/':
    //   return new Response('Anyone can access the homepage.');

    // case '/logout':
    //   // Invalidate the "Authorization" header by returning a HTTP 401.
    //   // We do not send a "WWW-Authenticate" header, as this would trigger
    //   // a popup in the browser, immediately asking for credentials again.
    //   return new Response('Logged out.', { status: 401 });

    default: {
      // The "Authorization" header is sent when authenticated.
      if (request.headers.has('Authorization')) {
        // Throws exception when authorization fails.
        const { user, pass } = basicAuthentication(request);
        if (verifyCredentials(user, pass)) {
            return await fetch(request)
        }
      } 
      // Not authenticated.
      return new Response('You need to login.', {
          status: 401,
          headers: {
            // Prompts the user for credentials.
            'WWW-Authenticate': 'Basic realm="Private Area", charset="UTF-8"',
          },
      });
    }

    case '/favicon.ico':
    case '/robots.txt':
      return new Response(null, { status: 204 });
  }

  return new Response('Not Found.', { status: 404 });
}

function verifyCredentials(user, pass) {
  if (BASIC_USER !== user || BASIC_PASS !== pass) {
      return false 
  }
  return true 
}

/**
 * Parse HTTP Basic Authorization value.
 * @param {Request} request
 * @throws {BadRequestException}
 * @returns {{ user: string, pass: string }}
 */
function basicAuthentication(request) {
  const Authorization = request.headers.get('Authorization');

  const [scheme, encoded] = Authorization.split(' ');

  // The Authorization header must start with Basic, followed by a space.
  if (!encoded || scheme !== 'Basic') {
    throw new BadRequestException('Malformed authorization header.');
  }

  // Decodes the base64 value and performs unicode normalization.
  // @see https://datatracker.ietf.org/doc/html/rfc7613#section-3.3.2 (and #section-4.2.2)
  // @see https://dev.mozilla.org/docs/Web/JavaScript/Reference/Global_Objects/String/normalize
  const buffer = Uint8Array.from(atob(encoded), character => character.charCodeAt(0));
  const decoded = new TextDecoder().decode(buffer).normalize();

  // The username & password are split by the first colon.
  //=> example: "username:password"
  const index = decoded.indexOf(':');

  // The user & password are split by the first colon and MUST NOT contain control characters.
  // @see https://tools.ietf.org/html/rfc5234#appendix-B.1 (=> "CTL = %x00-1F / %x7F")
  if (index === -1 || /[\0-\x1F\x7F]/.test(decoded)) {
    throw new BadRequestException('Invalid authorization value.');
  }

  return {
    user: decoded.substring(0, index),
    pass: decoded.substring(index + 1),
  };
}

function UnauthorizedException(reason) {
  this.status = 401;
  this.statusText = 'Unauthorized';
  this.reason = reason;
}

function BadRequestException(reason) {
  this.status = 400;
  this.statusText = 'Bad Request';
  this.reason = reason;
}
```

This is the domain to be protected:

{% embed url="https://worker.micsoftvn.com" %}

### Create worker services

Step 1:

<figure><img src="../../.gitbook/assets/Screenshot from 2023-04-24 17-13-10.png" alt=""><figcaption></figcaption></figure>

### Create trigger

Step 2: Create a trigger, add custom domain

<figure><img src="../../.gitbook/assets/Screenshot from 2023-04-24 17-16-19.png" alt=""><figcaption></figcaption></figure>

### Edit Services

Step 3: Edit Services, click Quick Edit button

<figure><img src="../../.gitbook/assets/Screenshot from 2023-04-24 17-14-54.png" alt=""><figcaption></figcaption></figure>

Save and deploy, then visit website : https://worker.micsoftvn.com

<figure><img src="../../.gitbook/assets/Screenshot from 2023-04-24 17-29-30.png" alt=""><figcaption></figcaption></figure>

REF link:

[https://workers.cloudflare.com\
https://developers.cloudflare.com/workers/examples/\
https://developers.cloudflare.com/workers/examples/basic-auth/](https://workers.cloudflare.comhttps/developers.cloudflare.com/workers/examples/https://developers.cloudflare.com/workers/examples/basic-auth/)
