<template>
    <vx-card class="h-full">
        <div class="p-8 login-tabs-container ">
            <div class="flex">

                <img src="@assets/images/logo/logo.png">
                <div class="font-extrabold">ECOMMERCE</div>
            </div>

            <div class="vx-card__title mt-4 mb-4">
                <h4 class="mb-4">Login</h4>
                <p>Welcome back, please login to your account.</p>
            </div>
            <div>
                <vs-input v-validate="'required|email|min:3'" data-vv-validate-on="blur" name="email" icon-no-border
                    icon="icon icon-user" icon-pack="feather" label-placeholder="Email" v-model="email" class="w-full" />
                <span class="text-danger text-sm">{{ errors.first('email') }}</span>

             <vx-input-group class="mb-base">
                <vs-input data-vv-validate-on="blur" v-validate="'required|min:6'"
                    :type="showPassword ? 'text' : 'password'" name="password"
                    icon-no-border icon="icon icon-lock" icon-pack="feather"
                    label-placeholder="Password" v-model="password"
                    class="w-full mt-6" />
                    <template slot="append">
            <div class="append-text mt-6 btn-addon">
          <vs-button icon-pack="feather" @click="showPassword = !showPassword" name="password" :icon="showPassword ? 'icon-eye' : 'icon-eye-off'"  ></vs-button>
        </div>
      </template>
                <span class="text-danger text-sm">{{ errors.first('password') }}</span>
            </vx-input-group>
                <div class="flex flex-wrap justify-between my-5">
                </div>
                <vs-button type="border" to="/register">Register</vs-button>
                <vs-button class="float-right" @click.prevent="submitForm">Login</vs-button>
            </div>
        </div>
    </vx-card>
</template>

<script>
import axios from '../../axios';

export default {

    data() {
        return {
            email       : "",
            password    : "",
            showPassword: false
        }
    },

    methods: {
        submitForm() {
            this.$validator.validateAll().then(async result => {
                if (result) {
                    const result = await axios.get(`http://localhost:3000/login?email=${this.email}&password=${this.password}`)
                    if (result.status == 200 && result.data.length > 0) {
                        console.log(result.data, 'login');
                        this.$vs.notify({ iconPack: 'feather', icon: 'icon-check-circle', text: 'Login Successful', color: 'success', position: 'top-right' })
                        const userType = result.data[0]
                        console.log(userType);
                        const loginData = {
                            email: userType.email,
                            username: userType.username,
                            password: userType.password,
                            islogin: true,
                            role: userType.role,
                            id: userType.id
                        }
                        localStorage.setItem("user-info", JSON.stringify(loginData));
                        const type = userType.role

                        if (type == 'user') {
                            this.$router.push({
                                name: "home",
                                params: { data: userType }
                            });
                        }
                        else if (type == 'admin') {
                            this.$router.push({
                                name: "admin-partner-list",
                                params: { data: userType }
                            });
                        }
                        else {
                            this.$router.push({
                                name: "partner-product",
                                params: { id: userType.id }
                            });
                        }
                    }
                    else {
                        this.$vs.notify({ text: 'Login Failed Icorrect Login details', color: 'danger', position: 'top-right' })
                    }
                }
            })
        },


    }
}

</script>

<style lang="scss">
#page-login {
    .social-login-buttons {
        .bg-facebook {
            background-color: #1551b1
        }

        .bg-twitter {
            background-color: #00aaff
        }

        .bg-google {
            background-color: #4285F4
        }

        .bg-github {
            background-color: #333
        }
    }
}</style>
