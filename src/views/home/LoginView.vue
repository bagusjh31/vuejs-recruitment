<template>
    <section class="page-section" id="contact">
        <div class="container px-4 px-lg-5">
            <div class="row gx-4 gx-lg-5 justify-content-center">
                <div class="col-lg-8 col-xl-6 text-center">
                    <h2 class="mt-0">Login</h2>
                    <hr class="divider" />
                    <p class="text-muted mb-5">Siap untuk melamar kerja dengan kami? Login Sekarang!</p>
                </div>
            </div>
            <div class="row gx-4 gx-lg-5 justify-content-center mb-5">
                <div class="col-lg-6">
                    <form id="contactForm" @submit.prevent="login">
                        <!-- Name input-->
                        <div class="form-floating mb-3">
                            <input class="form-control" id="username" type="text" placeholder="Masukkan Email"
                                v-model="state.email" />
                            <span v-if="v$.email.$error"> {{ v$.email.$errors[0].$message }} </span>
                            <label for="name">Email</label>
                        </div>
                        <div class="form-floating mb-3">
                            <input class="form-control" id="pass" type="password" placeholder="Masukkan Password"
                                v-model="state.password" />
                            <span v-if="v$.password.$error"> {{ v$.password.$errors[0].$message }} </span>
                            <label for="name">Password</label>
                        </div>
                        <div class="d-grid"><button type="submit" class="btn btn-primary btn-xl"
                                id="submitButton">Login</button></div>
                    </form>
                </div>
            </div>
            <div class="row gx-4 gx-lg-5 justify-content-center">
                <div class="col-lg-4 text-center mb-5 mb-lg-0">
                    <p>Belum Terdaftar? <router-link to="/signup">Signup</router-link></p>
                    <i class="bi-phone fs-2 mb-3 text-muted"></i>
                    <div>+62 8xx xxx xxx</div>
                    <div class="d-grid"><router-link to="/" class="btn btn-primary btn-sm mt-3">Kembali ke
                            Beranda</router-link></div>
                </div>
            </div>
        </div>
    </section>
</template>

<script>
import '@/load/home'

import axios from 'axios'
import Swal from 'sweetalert2'
import useValidate from '@vuelidate/core'
import { useRouter } from 'vue-router'
import { reactive, computed } from 'vue'
import { required, minLength, email } from '@vuelidate/validators'
import baseUrl from '@/utils/baseUrl'

export default {
    setup() {
        const router = useRouter()

        let data = localStorage.getItem('token')

        if (data) {
            router.push({
                name: 'Dashboard'
            })
        }

        const state = reactive({
            email: '',
            password: '',
        })

        const rules = computed(() => {
            return {
                email: { required, email },
                password: { required, minLength: minLength(6) },
            }
        })

        function login() {
            let email = state.email
            let password = state.password

            axios.post(`${baseUrl}/api/login`, {
                email: email,
                password: password
            }).then(res => {
                state.email = '',
                    state.password = '',

                    localStorage.setItem('user', JSON.stringify(res.data.user))
                localStorage.setItem('level', JSON.stringify(res.data.level))
                localStorage.setItem('token', res.data.content.access_token)

                if (JSON.stringify(res.data.user.level) == 1) {
                    router.push({
                        name: 'Adminboard',
                    })
                } else {
                    router.push({
                        name: 'Dashboard',
                    })
                }

                Swal.fire(
                    'Success',
                    'Login Success',
                    'success'
                )
            }).catch(() => {
                state.password = '',

                    Swal.fire(
                        'Failed',
                        'Login Failed!',
                        'error'
                    )
            })
        }

        const v$ = useValidate(rules, state)
        return { state, v$, login }
    },
    validations() {
        return {
            user: { required },
            password: { required },
        }
    },
}
</script>

<style>

</style>