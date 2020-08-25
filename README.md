# WrapItUp Parser

WrapItUp's Web Parser is a Node.js and Express based REST API End point that extracts meaningful content from a webpage and returns as a REST JSON. The API comes into play with WrapItUp 5 (TBA).

While the idea of open sourcing the backend is something I haven't considered yet, given this parser needs a lot of custom parsers for certain websites, there is a lot of work that needs to be done.

The API doesn't log any requests unless it encounters an error. The idea is to be _private by design_, if the requests are successful there is no need to log specifics of the request. In the event of an error, the entire request, originating device specifics (like OS version) are logged and are retained for up to 30 days.

_The API currently rejects any calls that are made from a non-WrapItUp origin._

If you feel you have a requirement to use a parser, please get in touch with me over on twitter [@kinngh](https://twitter.com/kinngh) and we can work out the specifics.

## Tech Stack
- Node.js
- Logflare (Log drain)
- Vercel / Netlify / AWS (Serverless functions)
- MongoDB (Caching requests)

## API Documentation

### Calling the API
The API is a serverless function, hosted on `REDACTED`. The end point is available at `domain.com/api?url=`, where the `url` parameter takes a link to a webpage, with or without `http`, `https` or `www.`.

### Successful call
```
{
    "title": "Refusing $20M in acquisition, 1.5M+ users and the story of how I built it all for $99/year",
    "publishDate": "2017-11-20T18:20:02.862Z",
    "wordCount": 1194,
    "summary": "I can go on about how I got the idea and how I built it all over again, but I’ll directly cut to what we’re here for. How I did all of it…",
    "baseUrl": "medium.com",
    "url": "https://medium.com/@kinngh/refusing-20m-in-acquisition-1-5m-users-and-the-story-of-how-i-built-it-all-for-99-year-1351e624e62f",
    "featuredImage": "https://miro.medium.com/max/1200/1*t7sM4fxibFbzO3eAkbx3Wg.jpeg",
    "content": "<div><article>Content</article></div>"
}
```

- `title`: `String`, Title of the web page.
- `publishDate`: `String` | `YYYY-MM-DD`T`HOURS:MINUTES:SECONDS:MSECONDS`, Date and Time stamp of when the article was published.
- `wordCount`: `Int` | Total word count, separated by word space and punctuations.
- `summary`: `String`, An excerpt of the webpage.
- `baseUrl`: `String`, The domain where the webpage is hosted.
- `url`: `String`, The full URL of the webpage.
- `featuredImage`:`URL`, Direct link to featured image. Calculated by taking into consideration the first image that has >30% coverage.
- `content`: `String`, The actual content of the webpage, after stripping scripts and other content. Returns as HTML to support Attributed Strings. Alternatively, the HTML tags can be stripped off with Regex.

### Error

While the chances of an error are extremely small, given URL validation happens on device, there is still a possibility that while the URL is valid, the server may be unresponsive.

```
{
  "statusCode" : 404,
  "error" : "Specified URL was not found."
}
```
- `statusCode`: `Int`, Status code of resulting error.
- `error`: `String`, Error message.
