<template>
    <div class="col-lg-12 col-md-12">
        <div class="card shadow-sm">
            <div class="card-head">
                <h1>Update Password</h1>
            </div>
            <div class="card-body">
                <form class="py-2">
                    <div class="alert alert-success" v-if="state.successMessage">
                        {{ state.successMessage }}
                    </div>
                    <div class="row my-3">
                        <div class="col-lg-6 col-md-12 mb-4">
                            <label for="" class="form-label">Password</label>
                            <span class="d-block text-danger font-weight-bold animate__animated animate__fadeIn"
                                v-if="v$.password.$error">* &nbsp;{{
                                        v$.password.$errors[0].$message
                                }}</span>
                            <input type="password" class="form-control" autocomplete v-model="state.password" />
                        </div>
                        <div class="col-lg-6 col-md-12">
                            <label for="" class="form-label">Re Type Your Password</label>
                            <span class="d-block text-danger font-weight-bold animate__animated animate__fadeIn"
                                v-if="v$.confirm_password.$error">* &nbsp;{{
                                        v$.confirm_password.$errors[0].$message
                                }}</span>
                            <input type="password" class="form-control" autocomplete  v-model="state.confirm_password" />
                        </div>
                    </div>
                    <button class="btn" @click.prevent="handlePassword">Update Password</button>
                </form>
            </div>
        </div>
    </div>

</template>

<script>
import axios from 'axios';
import useVuelidate from '@vuelidate/core'
import { reactive, computed } from 'vue';
import { mapGetters } from 'vuex';
import { helpers, minLength, required, sameAs, requiredIf } from '@vuelidate/validators'
export default {
    computed: mapGetters({
        user: 'auth/GET_USER'
    }),

    setup() {

        const state = reactive({
            password: '',
            confirm_password: '',
            successMessage: '',

        });
        const rules = computed(() => {
            return {
                password: {
                    required: helpers.withMessage('The password field is required', required),
                    minLength: helpers.withMessage('The password must be at least 8 characters', minLength(8)),
                },
                confirm_password: {
                    requiredIf: helpers.withMessage('Please Retype Your Password', requiredIf(state.password)),
                    sameAs: helpers.withMessage('The password and confirmation password do not match.', sameAs(state.password)),
                },
            }
        });
        const v$ = useVuelidate(rules, state);

        return { state, v$ };
    },
    methods: {

        handlePassword() {
            this.v$.$validate();
            if (!this.v$.$error) {
                axios.post('auth/password-update/' + this.user.uuid, {
                    'password': this.state.password,
                }).then(response => {
                    this.state.successMessage = response.data.message
                })
            }

        }
    }
}
</script>

<style lang="scss" scoped>
main {
    width: 100vw;
    min-height: 100vh;
    height: auto;
    overflow-x: hidden;
}

.container {
    overflow-x: hidden;

    .card {
        border-radius: 20px;
        overflow: hidden;
        z-index: 1;

        .card-head {
            height: 200px;
            background-image: url('@/assets/Dashboard/MyProfile/banner.jpg');
            background-size: cover;
            background-position: center center;
            display: flex;
            align-items: center;
            justify-content: center;

            h1 {
                color: #ffff;
                font-weight: 900;
            }
        }

        .card-body {
            form {
                input {
                    border: none;
                    border-bottom: 2px solid var(--dashboard-color);
                    outline: 0;

                    &::placeholder {
                        font-weight: 400;
                        color: var(--dashboard-color);
                    }

                    &:focus {
                        outline: 0;
                        box-shadow: none;
                        transition: all .2s ease-in-out;
                        border-bottom: 2px solid var(--dashboard-warning);

                    }

                }

                label {
                    font-weight: 700;
                }

                .btn {
                    background: var(--dashboard-color);
                    color: #fff;
                    border-radius: 5px;
                    font-weight: 800;
                }
            }
        }
    }
}
</style>