<template>
  <div>
    <div id="hcap-script" />
  </div>
</template>
<script>
const CaptchaScript = (cb) => {
    let script = document.createElement("script");
    script.src = "https://hcaptcha.com/1/api.js?render=explicit";
    script.async = true;
    
    script.addEventListener('load', cb, true);

    return script;
};

module.exports = {
    name: "VueHcaptcha",
    props: {
        sitekey: {
            type: String,
            required: true
        },
        theme: {
            type: String
        },
        size: {
            type: String
        },
        tabindex: {
            type: String 
        },
        popupclass: {
            type: String,
            default: ''
        }
    },
    mounted() {
        if (typeof window.hcaptcha === 'undefined') { //if not loaded, create script tag, and wait to render hcaptcha element
            let script = CaptchaScript(this.onloadScript);
            let container = document.getElementById("hcap-script");

            if (document.getElementsByTagName('head').length > 0) {
                container = document.getElementsByTagName('head')[0];
            }

            container.appendChild(script);  //append this here, this appends the tag to the start of the app.
        }
        else {
            this.onloadScript();
        }
    },
    methods: {
        onloadScript() {
            //set options for VueHcaptcha to be passed to the onload script
            let opt = {
                sitekey: this.sitekey,
                theme: this.theme ? this.theme : '',
                size: this.size ? this.size : '',
                tabindex: this.tabindex ? this.tabindex : '',
                callback: this.onVerify,
                "expired-callback": this.onExpired,
                "error-callback": this.onError
            }
            // Render hCaptcha widget and provide neccessary callbacks
            if (typeof window.hcaptcha !== 'undefined') {
                let hcaptcha = window.hcaptcha; // convienence var to access 
                let container = this.$slots.default ? this.$el.children[0] : this.$el;
                this.$widgetId = hcaptcha.render(container, opt);
            }

            // act like official hcapthca if no popup class is required
            if(this.popupclass && this.popupclass.length) {
                // hcaptcha creates both checkbox and challenge iframes right away
                // hacky hack to make sure hcaptha's iframes are added to the DOM
				setTimeout(() => {
					let frames = document.getElementsByTagName("iframe");
					for(var i = 0 ; i < frames.length ; i ++) {
						var f = frames[i];
						if(f.src.indexOf('hcaptcha-challenge') == -1) continue;
                        // as far as I can see, challenge is created wiht body being its (wrapper) parent: body > div > div > iframe
                        // verify if we have 3 parents and the 3rd one is 'body' before adding required class
						if(f.parentElement && f.parentElement.parentElement && f.parentElement.parentElement.parentElement) {
							if(f.parentElement.parentElement.parentElement.nodeName.toLowerCase() !== 'body') continue;
							if(f.parentElement.parentElement.className.indexOf(this.popupclass) == -1)
								f.parentElement.parentElement.className += this.popupclass + ' ';
						}
					}
				}, 250);
			}
        },
        onError(e) {
            if (window.hcaptcha === 'undefined') {
                return;
            }

            this.$emit('error',e);
            this.reset(); //reset the captcha
        },
        //let user handle the errors, etc
        onVerify(response) {
            this.$emit('verify', response);
        },
        onExpired() {
            this.$emit('expired');
        },
        execute() {
            window.hcaptcha.execute(this.$widgetId);
        },
        reset() {
            window.hcaptcha.reset(this.$widgetId);
        }
    }
}
</script>
