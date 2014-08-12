CDN-Usage
=========

Determine CDN benefits to host libraries

Does Using Google Libraries API CDN give you Performance Benefits? - 
See more at: http://www.root777.com/appdev/does-using-google-libraries-api-cdn-give-you-performance-benefits/

A CDN – short for Content Distribution Network helps serve content with high availability and provides performance benefits along with faster page load times. The Google Libraries API is a CDN for serving the most popular, open-source JavaScript libraries and allows any website to use it for free. Examples include Dojo, jQuery, Prototype among others.
Why use a CDN?

The greatest benefit is from caching. The theory is that if a visitor visited a site that was loading their JavaScript libraries, say jQuery for example from the Google CDN, then when they visit your website, the library is already in that user’s browser cache and will not have to be downloaded again. This sounds great in theory. I decided to put this theory to the test for the popular jQuery library and how likely is it where a visitor will arrive at your site with a cache for that JavaScript file.

Tracking Performance with HTTP Archive

In order to track the speed of the web over time, Google built and developed the HTTP Archive as an open source service. They transitioned the ownership and maintenance of it to the Internet Archive. It is a permanent repository of web performance information such as page size, requests made, and technologies utilized. Their list of URLs is based solely on the Alexa Top 1,000,000 sites. As of March 2012, there were a total of 77082 sites analyzed. This count is expected to ramp up to cover the top 1 Million websites on the Internet soon.

Google Libraries API CDN
Running analysis of the HTTP Archive for the usage of Google Libraries API, we got a count of 14345 sites of the 77082 sites tested. This is almost 18.61% which is quite impressive. However, this count does not account for the different libraries and their versions. This is important as sites must reference the exact same CDN URL from either Google or Microsoft or any other CDN to obtain the cross-site caching benefits.
In case you were curious, the Microsoft CDN is only used in 157 of the sites tested or only 0.2% of the most popular websites on the Internet leverage it compared to the 18.61% that leverage the Google Libraries API CDN.
Sites using Google Libraries API Sites using Google Libraries API

Test 1:  Validate the Google APIs reference
We want to verify the 18.61% of the pages. This count of 14345 sites comes from an HTTP request containing ‘googleapis.com’ anywhere in the URL. The query used to extract this particular statistic was
SELECT COUNT(DISTINCT pageid)
FROM requests
WHERE url LIKE '%googleapis.com%';
Google API reference in HTTP Archive

Test 2: Determine the percentage of pages that ran a jQuery version
Next, we want to determine the percentage of pages that was using atleast some version of  jQuery from the list of the sites in the HTTP Archive. Running the following SQL query gives us the result of 11145 or 14.45% of the total sites analyzed.
SELECT COUNT(DISTINCT pageid) 
FROM requests 
WHERE url LIKE 'http%://ajax.googleapis.com/ajax/libs/jquery/%';
jQuery Count in HTTP Archive

Test 3: Determine jQuery versions
Next, we want to determine the breakdown for each distinct URL that represented jQuery. Again, this is the statistic that would determine the cross-site caching benefit. Running the following query gives us the breakdown below:
SELECT url, COUNT(DISTINCT pageid) AS count
FROM requests 
WHERE url LIKE 'https://ajax.googleapis.com/ajax/libs/jquery/%' 
GROUP BY url 
ORDER BY count DESC;
Version	Protocol	%age	Count
1.4.2	http	2.19%	1695
1.3.2	http	1.48%	1142
1.7.1	http	1.34%	1039
1.6.2	http	0.69%	533
1.5.2	http	0.68%	529
1.4.4	http	0.61%	474
1.6.1	http	0.58%	450
1.6.4	http	0.53%	414
1.7.1	https	0.47%	366
1.4	http	0.40%	316
This proves that the fragmentation issues are very real. This reveals that the most popular URL for loading jQuery was jQuery 1.4.2 via http. This constitutes only 2.19% (1695 out of 77082) of all the websites tested. The next most popular is jQuery 1.3.2 with 1.48% (1142 out of 77082) and so on. It is not just the fragmentation of the different libraries used, but also the different protocols (e.g. http vs. https) since they are cached separately.
Another frustration is the “latest version” reference results. This allows you to request either version 1 or 1.x and automatically receive 1.x.y version.You can see the full output of the above query posted on GitHub if you are interested.

Conclusion & Recommendations

So, is using a CDN like Google Libraries API really provide you the performance boost via cross-site caching? The answer is most conclusively proven to be NO and is not likely to benefit your first time visitors. The assumption that many of your website’s first time users will have a JavaScript cached – because they happen to visit another site that uses the exact same version of the same JavaScript library is wrong as shown by the fragmented nature of the web above. It also depends on how likely your visitor target demographic matches that of the sites using the CDN.
You are most likely better off bundling jQuery with the rest of your site’s JavaScript
Make sure to use the Expires header to make HTTP requests cacheable since for your repeat visitors, it doesn’t matter where the file was served from
Browser users leveraging privacy options that clear the cache (e.g. privacy.clearOnShutdown.cache option) between browser sessions cannot leverage the assumed benefits of a CDN distribution
Another point of note coming from Steve Souders is that the amount of disk space for caching has not caught up with people’s usage of the web. For example, IE has a default cache size of 8 – 50 MB, Firefox has 50MB, Opera has 20MB and Chrome has ~80MB. Mobile phones have an even smaller size limit. This cache will eventually fill up and when it happens, the FIFO (first-in-first-out) rule applies where cached resources need to make way for new ones.
If websites are using the https reference to a JS library, the browsers usually default to not caching those files to disk when they are retrieved using SSL.
If you are still considering experimenting using the Google Libraries API or another CDN for libraries, use something like Yahoo’s Boomerang. This piece of JavaScript measures a whole bunch of performance characteristics of your user’s web browsing experience. All you have to do is stick it into your web pages and call the init() method.
