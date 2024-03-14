<template>
    <vx-card class="h-full">
        <div class="px-8 pt-8 register-tabs-container">
            <div class="flex">
                <img src="@assets/images/logo/logo.png">
                <div class="font-extrabold">ECOMMERCE</div>
            </div>
            <div class="vx-card__title mt-4 mb-4">
                <h4 class="mb-4">Create Account</h4>
                <p>Fill the below form to create a new account.</p>
            </div>
            <vs-tabs>
                <vs-tab label="Register">
                    <vs-button color="danger" type="flat"></vs-button>
 
                    <form>
                        <div class="vx-row ">

                            <div class="vx-col w-full">
                                <vs-input size="large" v-validate="'required|alpha'" placeholder="Your Name" name="name"
                                    v-model="name" class="mt-5 w-full" data-vv-validate-on="blur" />
                                <span class="text-danger text-sm" v-show="errors.has('name')">{{ errors.first('name')
                                }}</span>
                            </div>

                            <div class="vx-col w-1/2">
                                <vs-input size="large" v-validate="'required|alpha_dash'" placeholder="Your Username"
                                    name="username" data-vv-validate-on="blur" v-model="username" class="mt-5 w-full" />
                                <span class="text-danger text-sm" v-show="errors.has('username')">{{
                                    errors.first('username') }}</span>
                            </div>

                            <div class="vx-col w-1/2">
                                <vs-input size="large" @input="checkEmail(email)" v-validate="'required|email'" data-vv-validate-on="blur"
                                    placeholder="Your Email" name="email" v-model="email" class="mt-5 w-full" />
                                <span class="text-danger text-sm" v-show="errors.has('email')">{{ errors.first('email')
                                }}</span>
                            </div>

                            <div class="vx-col w-1/2">
                                <vs-input type="password" size="large" v-validate="'required|min:6|max:10'" ref="password" data-vv-validate-on="blur"
                                    placeholder="Your Password" name="password" v-model="password" class="mt-5 w-full" />
                                <span class="text-danger text-sm" v-show="errors.has('password')">{{
                                    errors.first('password') }}</span>
                            </div>

                            <div class="vx-col w-1/2">
                                <vs-input type="password" size="large"
                                    v-validate="'required|min:6|max:10|confirmed:password'" data-vv-as="password"
                                    placeholder="Confirm Password" name="confirm_password" v-model="confirm_password"
                                    class="mt-5 w-full" />
                                <span class="text-danger text-sm" v-show="errors.has('confirm_password')">{{
                                    errors.first('confirm_password') }}</span>
                            </div>
                        </div>
                        <div class="flex">

                            <vs-button type="filled" @click.prevent="registerUser" class="mt-5 block">Submit</vs-button>
                            <vs-button type="border" to="/login" class="ml-4 mt-5 block">Login</vs-button>
                        </div>
                    </form>
                </vs-tab>
            </vs-tabs>
        </div>
    </vx-card>
</template>

<script>

import axios from   '../../axios'
export default {

    data() {
        return {
            activeSuccess: false,
            active: false,
            username: '',
            email: '',
            password: '',
            confirm_password: '',
            isTermsConditionAccepted: true,
            role: 'user',
            name: ''
        }
    },

    methods: {
        async checkEmail() {
            console.log(this);
            const res = await axios.get(`http://localhost:3000/login?email=${this.email}`);
            if (res.status == 200 && res.data.length > 0) {
                    this.$vs.notify({ title: 'email already exist', text: 'email already exists', color: 'danger',position:'top-right' })
            } else {
                this.registerUser
            }
        },

        registerUser() {
            this.$validator.validateAll().then(async result => {
                if (result) {
                    // if form have no errors
                    const check = await axios.get(`http://localhost:3000/login?email=${this.email}&password=${this.password}`);
                    if (check.status == 200 && check.data.length > 0) {
                        this.$vs.notify({iconPack: 'feather',icon:'alert', title: 'user with this already exist', text: 'email already exists', color: 'danger',position:'top-right' })
                    }
                    else {
                        const result = await axios.post(`http://localhost:3000/login`, {
                            email: this.email,
                            password: this.password,
                            username: this.username,
                            role: 'user',
                            name: this.name
                        })
                        console.log(result,'status');
                        if (result.status == 201) {
                            console.log(result,'result');
                            localStorage.setItem("user-info", JSON.stringify(result.data));
                            this.$vs.notify({ iconPack: 'feather',icon:'check', text: 'User Registered successfully', color: 'success',position:'top-right' })
                            this.$router.push('/login')
                        }

                    }
                   
                } 
            })
        }
    }

}

</script>
