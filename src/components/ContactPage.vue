<template>
    <section id="contact">
        <div class="container-fluid py-md-3" id="contact">
            <h2 class="fw-bold text-center section-color">CONTACT</h2>
            <div class="row">
                <div class="col-12 col-md-6">
                    <iframe src="https://www.google.com/maps/embed?pb=!1m18!1m12!1m3!1d31457.89589287348!2d118.73146129999999!3d9.745982600000001!2m3!1f0!2f0!3f0!3m2!1i1024!2i768!4f13.1!3m3!1m2!1s0x33b563fa65f9d5d7%3A0xe6a961ced3677c2e!2sPuerto%20Princesa%20International%20Airport!5e0!3m2!1sen!2sph!4v1755866607429!5m2!1sen!2sph" 
                     height="450"  class="border-0 w-100" allowfullscreen="" loading="lazy" referrerpolicy="no-referrer-when-downgrade">

                    </iframe>
                </div>
                <div class="col-12 col-md-6">
                    <div id="form-col">
                        <form class="p-3 rounded"  @submit.prevent="submitForm">
                            <div class="form-group pb-3">
                                <input type="text" v-model="name" class="form-control" id="name" placeholder="First Name M.I. Last Name">

                            </div>

                            <div class="form-group pb-3">
                                <input type="email" v-model="email" class="form-control" id="email" placeholder="Email">
                                
                            </div>

                            <div class="form-group pb-3">
                                <textarea v-model="message" class="form-control" id="message" rows="7" placeholder="Message"></textarea>
                            </div>


                            <div class="form-footer">
                                <div class="social-icons">
                                    <a href="https://www.linkedin.com/in/yourprofile" target="_blank" class="">
                                        <img src="https://encrypted-tbn0.gstatic.com/images?q=tbn:ANd9GcRokEYt0yyh6uNDKL8uksVLlhZ35laKNQgZ9g&s" alt="" height="30px">
                                    </a>

                                    <a href="https://github.com/yourusername" target="_blank">
                                        <img src="https://gitlab.com/uploads/-/system/project/avatar/278964/project_avatar.png" alt="" height="30px">
                                    </a>

                                    <a href="https://github.com/yourusername" target="_blank">
                                        <img src="https://github.githubassets.com/assets/GitHub-Mark-ea2971cee799.png" alt="" height="30px">
                                    </a>
                                </div>

                                <!-- <div class="d-flex justify-content-end mt-2"> -->
                                    <button type="submit" class="btn btn-outline-light my-3  submit-btn" id="submit" :disabled="isLoading">
                                        {{ isLoading ? "Sending.." : "Submit" }}
                                    </button>

                                    <div class="d-flex justify-content-end mt-2">
                                        <div ref="recaptchaContainer"></div>
                                    </div>
                                <!-- </div> -->
                            </div>
                        </form>
                    </div>
                    
                </div>
            </div>
            
        </div>

    </section>
</template>

<script setup>
    import { onBeforeMount, onBeforeUnmount, onMounted, ref } from 'vue';
    import { Notyf } from 'notyf';
    import 'notyf/notyf.min.css'

    const notyf = new Notyf();

    const WEB3FORMS_ACCESS_KEY = "9775af05-2e1d-44e3-b5b5-0460cc832342";
    const subject = "New message from Portfolio Contact Form";

    const name = ref("");
    const email = ref("");
    const message = ref("");

    const isLoading = ref(false);

    const submitForm = async() => {

        if (!recaptchaToken.value) {
            notyf.error("Please verify that you are not a robot");
            return;
        }
        isLoading.value = true;
        try {
            const response = await fetch("https://api.web3forms.com/submit", {
                method: 'POST',
                headers: {
                    "Content-Type": "application/json",
                    "Accept": "application/json"
                },
                body: JSON.stringify({
                    access_key: WEB3FORMS_ACCESS_KEY,
                    subject: subject,
                    name: name.value,
                    email: email.value,
                    message: message.value
                })
            });

            const result = await response.json();
            if (result.success) {
                console.log(result);
                isLoading.value = false;
                notyf.success("Message sent!");
                name.value = "";
                email.value = "";
                message.value = "";
            }
        } catch (error) {
            console.log(error);
            isLoading.value = false;
            notyf.error("FAiled to send message");            
        } finally {
            resetRecaptcha();
        }
    }

    const SITE_KEY= '6LcE-hEsAAAAAHej31bxiWBMItsYfMVMicWtSXUm'
    const recaptchaContainer = ref(null);
    const recaptchaWidgetId = ref(null);
    const recaptchaToken = ref('');

    function onRecaptchaSuccess(token) {
        recaptchaToken.value = token;
    }

    function onRecaptchaExpired() {
        recaptchaToken.value = "";
    }

    function renderRecaptcha() {
        if(!window.grecaptcha) {
            console.error('recaptcha not loaded');
            return;
        }

        recaptchaWidgetId.value = window.grecaptcha.render(
            recaptchaContainer.value, {
                sitekey: SITE_KEY,
                size: 'normal',
                callback: onRecaptchaSuccess,
                'expired-callback': onRecaptchaExpired
            }
        )

    }

    function resetRecaptcha() {
        if(recaptchaWidgetId.value != null) {
            window.grecaptcha.reset(recaptchaWidgetId.value);
            recaptchaToken.value = '';
        }
    }

    onMounted(() => {
        const interval = setInterval(() => {
            if (window.grecaptcha && window.grecaptcha.render) {
                renderRecaptcha();
                clearInterval(interval);
            }
        }, 100)

        onBeforeUnmount(() => {
            clearInterval(interval);
        })
    })
</script>
