# Header Bidding System

This repository contains a simplified header bidding system implementation using Prebid.js and Google Publisher Tags (GPT). The system is designed to optimize ad revenue by enabling header bidding and integrating multiple SSPs (Supply-Side Platforms) to maximize competition for ad inventory.

## Features

- **Prebid.js Integration**: Implements Prebid.js to enable header bidding.
- **Multiple SSPs**: Configures demand partners (OpenX, Sovrn, Xandr) to increase competition.
- **Responsive Ad Units**: Supports responsive designs across mobile, tablet, and desktop.
- **Dynamic Floor Pricing**: Implements a dynamic floor pricing mechanism based on ad size and device type.
- **Analytics Integration**: Tracks bids, win rates, and latency metrics using Google Analytics.
- **Error Handling**: Logs and handles errors when SSP bids fail, timeout, or return invalid responses.
- **Fallback Ads**: Provides fallback ads when no bids are received or all bids fail.
- **Bid Validation**: Validates incoming bids based on the OpenRTB protocol.
- **Lazy Loading Ads**: Optimizes performance by lazy-loading ads only when they are in-view.

## Setup

1. **Include Prebid.js in your HTML file:**
   ```html
   <script type="text/javascript" src="https://cdn.jsdelivr.net/npm/prebid.js@latest/dist/prebid.min.js"></script>
   ```

2. **Initialize Prebid.js and Configure SSPs:**
   ```javascript
   var PREBID_TIMEOUT = 2000;
   var adUnits = [
     {
       code: 'div-gpt-ad-header-0',
       sizes: [[728, 90], [970, 250]],
       bids: [
         {
           bidder: 'openx',
           params: { tagid: '12345' }
         },
         {
           bidder: 'sovrn',
           params: { tagid: '67890' }
         }
       ]
     }
   ];
   var pbjs = pbjs || {};
   pbjs.que = pbjs.que || [];
   ```

3. **Define Ad Units for Responsive Designs:**
   ```javascript
   if (window.innerWidth >= 1024) { 
     // Desktop ad units
   } else if (window.innerWidth > 720) { 
     // Tablet ad units
   } else { 
     // Mobile ad units
   }
   ```

4. **Integrate and Test Prebid Adapters:**
   ```javascript
   pbjs.que.push(function() {
     pbjs.addAdUnits(adUnits);
     pbjs.requestBids({
       bidsBackHandler: sendAdserverRequest
     });
   });
   ```

5. **Implement Dynamic Floor Pricing:**
   ```javascript
   const customConfigObject = {
     buckets: [
       {
         precision: 2,
         min: 0,
         max: 20,
         increment: 0.05
       }
     ]
   };
   pbjs.setPriceGranularity(customConfigObject);
   ```

6. **Integrate Analytics:**
   ```html
   <script async src="https://www.google-analytics.com/analytics.js"></script>
   <script>
     ga('create', 'UA-XXXXX-Y', 'auto');
     ga('send', 'pageview');
   </script>
   ```

7. **Implement Error Handling:**
   ```javascript
   pbjs.onEvent('bidTimeout', function(bid) {
     console.error('Bid timed out:', bid);
   });
   pbjs.onEvent('bidWon', function(bid) {
     console.log('Bid won:', bid);
   });
   ```

8. **Implement Fallback Ads:**
   ```javascript
   function sendAdserverRequest() {
     if (pbjs.adserverRequestSent) return;
     pbjs.adserverRequestSent = true;
     googletag.cmd.push(function() {
       pbjs.setTargetingForGPTAsync();
       googletag.pubads().refresh();
     });
   }
   ```

9. **Validate Bids:**
   ```javascript
   pbjs.bidderSettings = {
     standard: {
       adserverTargeting: [
         { key: "hb_bidder", val: function(bidResponse) { return bidResponse.bidderCode; } },
         { key: "hb_adid", val: function(bidResponse) { return bidResponse.adId; } },
         { key: "hb_pb", val: function(bidResponse) { return bidResponse.pbHg; } }
       ]
     }
   };
   ```

10. **Lazy Load Ads:**
    ```javascript
    var observer = new IntersectionObserver(function(entries) {
      entries.forEach(function(entry) {
        if (entry.isIntersecting) {
          googletag.cmd.push(function() {
            googletag.pubads().refresh([entry.target]);
          });
          observer.unobserve(entry.target);
        }
      });
    });
    document.querySelectorAll('.ad-slot').forEach(function(adSlot) {
      observer.observe(adSlot);
    });
    ```

11. **CI/CD Pipeline Integration:**
    Create a GitHub Actions workflow to automate deployment:
    ```yaml
    name: CI/CD Pipeline

    on:
      push:
        branches:
          - main

    jobs:
      build:
        runs-on: ubuntu-latest
        steps:
          - uses: actions/checkout@v2
          - name: Set up Node.js
            uses: actions/setup-node@v2
            with:
              node-version: '14'
          - run: npm install
          - run: npm run build
          - name: Deploy to Server
            run: npm run deploy
    ```

## License
albertdesmond26@gmail.com
This project is licensed under the MIT License. See the [LICENSE](LICENSE) file for more information.
