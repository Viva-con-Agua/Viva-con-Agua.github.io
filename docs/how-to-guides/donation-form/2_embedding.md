# How to embedding an donation form into an website.

In the most cases the donation-form will be embedded as an iframe.

!!! important "Goal"

    At the end of this guide, you will have a donation-form running on a website.

!!! important "Prerequisites"

    This guide assumes that you have basic html and js skills.
    Get an donation_form_id by create an donation_form.

## Create onmessage

For handling javascript events, we define at first an `window.onmessage` block:

```
<script type="text/javascript">
    window.onmessage = function(e) {
        if (e.data == 'navigate') {
            var top = document.getElementById('donation-form').offsetTop;
            window.scrollTo(0, top);
        }
        if (e.data.event == 'gtm-trigger') {
            dataLayer?.push(e.data.data)
        }
    };
</script>
```
The `navigate` event is used for scroll to the top of the iframe. The `gtm-trigger` is used for handling GoogleTagManager events.

## 2) Resize Iframe

To ensure that the iframe always fits correctly into the page, we need another handler. 
```
<script type="text/javascript">
    //stores the iframe elemement. 
    var iframeElement = null

    //used after the iframe is loaded.
    const messageThatIframeIsLoaded = () => { 
        //assign the donation form element to iframeElement  
        iframeElement = document.getElementById('donation-form')
        if (iframeElement) {        
            //trigger the message event in the iframe.
            iframeElement.contentWindow?.postMessage({ type: 'iframe-loaded' }, '*')        
        }
    }
    //used for handle iframe resize.
    const handleIframeMessage = (event) => {        
        if (event.data.type === 'iframe-height') {        
            console.log('iframe event', event.origin, event.data.data)  
            //resize iframeElement      
            iframeElement.style.height = event.data.data.height +"px"
        }        
    }
    //add event listener for handling the function call via message event.  
    window.addEventListener('message', handleIframeMessage, false)        
</script>
```

## 3) Iframe

Finally, the iframe. The `donation_form_id` must be adjusted so that the correct campagne can be assigned.
The `messageThatIframeIsLoaded` function is defined in the onload interface of the iframe, so the iframe is set directly to the correct size during initial loading. 

```
<iframe id="donation-form" onload="messageThatIframeIsLoaded()" src="https://donation-form.vivaconagua.org/#/?donation_form_id=<donation_form_id>" style="width: 99%; border: none; height: 1765px;"></iframe>
```