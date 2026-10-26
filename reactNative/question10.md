# React Native Interview Question 10 : Image optimization (fast loading OR caching)

1. react native fast image 

5 important points : 
1. Native image-loading engines: Uses SDWebImage on iOS and Glide on Android rather than implementing the entire image-loading pipeline in JavaScript.
2. Memory and disk caching: Stores downloaded images in caches, helping reduce repeated network requests and improve loading speed when images are reused.
3. Prioritization: Supports priority values such as high, normal, and low, helping prioritize important images over less important ones.
4. Cache control: Supports cache policies such as immutable, web, and cacheOnly, allowing developers to control image reuse and network fetching behavior.
5. Better scrolling experience: Can improve image-heavy screens such as hotel listings, product catalogs, and social feeds by reducing redundant downloads and managing image loading efficiently.