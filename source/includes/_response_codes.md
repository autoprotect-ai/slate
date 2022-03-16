# Response Codes

| **Response Code** | **Definition** |
| --- | --- |
| **200 (OK)** | The request was processed as expected, and the response includes some data. |
| **204 (No Content)** | The request was processed as expected, but the response is empty. |
| **400 (Bad Request)** | A parameter, format, or structure of the request is incorrect. |
| **401 (Unauthorized)** | The authentication credentials are missing or incorrect. |
| **403 (Forbidden)** | The provided credentials do not have access to this endpoint or resource. |
| **404 (Not Found)** | Either the endpoint does not exist, or the requested resource does not exist. |
| **429 (Too Many Requests)** | The number of requests have exceeded the rate limit, see **Rate Limits**. |
| **5XX** | An uncaught server error has occurred. This usually indicates a system-level failure. Please contact us if you see this repeatedly. |