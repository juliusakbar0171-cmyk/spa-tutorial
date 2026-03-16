<template>
    <div class="login-root">
        <!-- Background layers -->
        <div class="bg-base"></div>
        <div class="bg-grid"></div>
        <div class="bg-glow glow-1"></div>
        <div class="bg-glow glow-2"></div>

        <!-- Card -->
        <div class="card">
            <!-- Brand -->
            <div class="brand">
                <div class="brand-icon">
                    <svg width="22" height="22" viewBox="0 0 24 24" fill="none" stroke="currentColor" stroke-width="2"
                        stroke-linecap="round" stroke-linejoin="round">
                        <rect x="2" y="3" width="20" height="14" rx="2" />
                        <path d="M8 21h8M12 17v4" />
                    </svg>
                </div>
                <span class="brand-name">radit </span>
            </div>

            <h1 class="heading">radit diddy</h1>
            <p class="subheading">Masuk untuk melanjutkan ke dashboard</p>

            <form @submit.prevent="login" class="form">
                <!-- Email -->
                <div class="field" :class="{ focused: focusedField === 'email' }">
                    <label class="label">Email</label>
                    <div class="input-wrap">
                        <svg class="input-icon" width="16" height="16" viewBox="0 0 24 24" fill="none"
                            stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <rect width="20" height="16" x="2" y="4" rx="2" />
                            <path d="m22 7-8.97 5.7a1.94 1.94 0 0 1-2.06 0L2 7" />
                        </svg>
                        <input v-model="form.email" type="email" placeholder="admin@kasir.com" required
                            class="input" @focus="focusedField = 'email'" @blur="focusedField = ''" />
                    </div>
                </div>

                <!-- Password -->
                <div class="field" :class="{ focused: focusedField === 'password' }">
                    <label class="label">Password</label>
                    <div class="input-wrap">
                        <svg class="input-icon" width="16" height="16" viewBox="0 0 24 24" fill="none"
                            stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <rect width="18" height="11" x="3" y="11" rx="2" ry="2" />
                            <path d="M7 11V7a5 5 0 0 1 10 0v4" />
                        </svg>
                        <input v-model="form.password" :type="showPass ? 'text' : 'password'" placeholder="••••••••"
                            required class="input" @focus="focusedField = 'password'"
                            @blur="focusedField = ''" />
                        <button type="button" class="eye-btn" @click="showPass = !showPass" tabindex="-1">
                            <svg v-if="!showPass" width="15" height="15" viewBox="0 0 24 24" fill="none"
                                stroke="currentColor" stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                                <path d="M2 12s3-7 10-7 10 7 10 7-3 7-10 7-10-7-10-7Z" />
                                <circle cx="12" cy="12" r="3" />
                            </svg>
                            <svg v-else width="15" height="15" viewBox="0 0 24 24" fill="none" stroke="currentColor"
                                stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                                <path d="M9.88 9.88a3 3 0 1 0 4.24 4.24" />
                                <path
                                    d="M10.73 5.08A10.43 10.43 0 0 1 12 5c7 0 10 7 10 7a13.16 13.16 0 0 1-1.67 2.68" />
                                <path
                                    d="M6.61 6.61A13.526 13.526 0 0 0 2 12s3 7 10 7a9.74 9.74 0 0 0 5.39-1.61" />
                                <line x1="2" x2="22" y1="2" y2="22" />
                            </svg>
                        </button>
                    </div>
                </div>

                <!-- Error -->
                <transition name="shake">
                    <div v-if="error" class="error-box">
                        <svg width="14" height="14" viewBox="0 0 24 24" fill="none" stroke="currentColor"
                            stroke-width="2" stroke-linecap="round" stroke-linejoin="round">
                            <circle cx="12" cy="12" r="10" />
                            <line x1="12" x2="12" y1="8" y2="12" />
                            <line x1="12" x2="12.01" y1="16" y2="16" />
                        </svg>
                        {{ error }}
                    </div>
                </transition>

                <!-- Button -->
                <button type="submit" :disabled="loading" class="submit-btn" :class="{ loading }">
                    <span v-if="!loading" class="btn-text">
                        Masuk
                        <svg width="16" height="16" viewBox="0 0 24 24" fill="none" stroke="currentColor"
                            stroke-width="2.5" stroke-linecap="round" stroke-linejoin="round">
                            <path d="M5 12h14M12 5l7 7-7 7" />
                        </svg>
                    </span>
                    <span v-else class="btn-loader">
                        <svg class="spin" width="18" height="18" viewBox="0 0 24 24" fill="none"
                            stroke="currentColor" stroke-width="2.5" stroke-linecap="round">
                            <path d="M21 12a9 9 0 1 1-6.219-8.56" />
                        </svg>
                        Memproses...
                    </span>
                </button>
            </form>

            <p class="footer-text">© 2026 Kasir Digital. All rights reserved.</p>
        </div>
    </div>
</template>

<script setup>
import { ref } from 'vue'
import { useRouter } from 'vue-router'
import api from '@/api/axios'

const router = useRouter()
const loading = ref(false)
const error = ref('')
const showPass = ref(false)
const focusedField = ref('')
const form = ref({ email: '', password: '' })

async function login() {
    loading.value = true
    error.value = ''
    try {
        const res = await api.post('/login', form.value)
        localStorage.setItem('token', res.data.data.token)
        localStorage.setItem('user', JSON.stringify(res.data.data.user))
        router.push('/')
    } catch (err) {
        error.value = err.response?.data?.message || 'Email atau password salah'
    } finally {
        loading.value = false
    }
}
</script>

<style scoped>
@import url('https://fonts.googleapis.com/css2?family=Plus+Jakarta+Sans:wght@400;500;600;700&display=swap');

* { box-sizing: border-box; }

.login-root {
    font-family: 'Plus Jakarta Sans', sans-serif;
    min-height: 100vh;
    display: flex;
    align-items: center;
    justify-content: center;
    position: relative;
    overflow: hidden;
}

/* Background */
.bg-base {
    position: fixed; inset: 0;
    background: #0a0f1e;
    z-index: 0;
}
.bg-grid {
    position: fixed; inset: 0;
    background-image:
        linear-gradient(rgba(99,102,241,.06) 1px, transparent 1px),
        linear-gradient(90deg, rgba(99,102,241,.06) 1px, transparent 1px);
    background-size: 48px 48px;
    z-index: 1;
}
.bg-glow {
    position: fixed;
    border-radius: 50%;
    filter: blur(100px);
    z-index: 1;
    pointer-events: none;
}
.glow-1 {
    width: 500px; height: 500px;
    background: radial-gradient(circle, rgba(99,102,241,.18) 0%, transparent 70%);
    top: -100px; left: -100px;
}
.glow-2 {
    width: 400px; height: 400px;
    background: radial-gradient(circle, rgba(236,72,153,.12) 0%, transparent 70%);
    bottom: -80px; right: -80px;
}

/* Card */
.card {
    position: relative;
    z-index: 10;
    width: 420px;
    background: rgba(15, 20, 40, 0.85);
    backdrop-filter: blur(24px);
    -webkit-backdrop-filter: blur(24px);
    border: 1px solid rgba(255,255,255,.08);
    border-radius: 24px;
    padding: 40px;
    box-shadow:
        0 0 0 1px rgba(99,102,241,.1),
        0 24px 64px rgba(0,0,0,.5),
        inset 0 1px 0 rgba(255,255,255,.06);
    animation: fadeUp .5s cubic-bezier(.16,1,.3,1) both;
}

@keyframes fadeUp {
    from { opacity: 0; transform: translateY(24px); }
    to   { opacity: 1; transform: translateY(0); }
}

/* Brand */
.brand {
    display: flex;
    align-items: center;
    gap: 10px;
    margin-bottom: 28px;
}
.brand-icon {
    width: 38px; height: 38px;
    background: linear-gradient(135deg, #6366f1, #818cf8);
    border-radius: 10px;
    display: flex; align-items: center; justify-content: center;
    color: white;
    box-shadow: 0 4px 16px rgba(99,102,241,.4);
}
.brand-name {
    font-size: 15px;
    font-weight: 700;
    color: rgba(255,255,255,.9);
    letter-spacing: -.02em;
}

/* Heading */
.heading {
    font-size: 26px;
    font-weight: 700;
    color: #ffffff;
    letter-spacing: -.03em;
    margin: 0 0 6px;
    line-height: 1.2;
}
.subheading {
    font-size: 13.5px;
    color: rgba(255,255,255,.4);
    margin: 0 0 28px;
}

/* Form */
.form { display: flex; flex-direction: column; gap: 16px; }

.field { display: flex; flex-direction: column; gap: 6px; }

.label {
    font-size: 12.5px;
    font-weight: 600;
    color: rgba(255,255,255,.5);
    letter-spacing: .04em;
    text-transform: uppercase;
    transition: color .2s;
}
.field.focused .label { color: #818cf8; }

.input-wrap {
    position: relative;
    display: flex;
    align-items: center;
}
.input-icon {
    position: absolute;
    left: 14px;
    color: rgba(255,255,255,.25);
    pointer-events: none;
    transition: color .2s;
}
.field.focused .input-icon { color: #818cf8; }

.input {
    width: 100%;
    background: rgba(255,255,255,.04);
    border: 1px solid rgba(255,255,255,.08);
    border-radius: 12px;
    padding: 13px 44px 13px 40px;
    color: rgba(255,255,255,.9);
    font-size: 14px;
    font-family: inherit;
    outline: none;
    transition: border-color .2s, background .2s, box-shadow .2s;
}
.input::placeholder { color: rgba(255,255,255,.2); }
.input:focus {
    border-color: rgba(99,102,241,.5);
    background: rgba(99,102,241,.06);
    box-shadow: 0 0 0 3px rgba(99,102,241,.12);
}

.eye-btn {
    position: absolute;
    right: 13px;
    background: none;
    border: none;
    cursor: pointer;
    color: rgba(255,255,255,.25);
    padding: 4px;
    display: flex; align-items: center;
    transition: color .2s;
}
.eye-btn:hover { color: rgba(255,255,255,.6); }

/* Error */
.error-box {
    display: flex;
    align-items: center;
    gap: 8px;
    background: rgba(239,68,68,.08);
    border: 1px solid rgba(239,68,68,.2);
    border-radius: 10px;
    padding: 10px 14px;
    color: #f87171;
    font-size: 13px;
    font-weight: 500;
}

/* Submit */
.submit-btn {
    margin-top: 4px;
    width: 100%;
    padding: 14px;
    background: linear-gradient(135deg, #6366f1 0%, #818cf8 100%);
    color: white;
    font-family: inherit;
    font-size: 14.5px;
    font-weight: 700;
    letter-spacing: .01em;
    border: none;
    border-radius: 12px;
    cursor: pointer;
    transition: transform .15s, box-shadow .15s, opacity .15s;
    box-shadow: 0 4px 20px rgba(99,102,241,.35);
}
.submit-btn:hover:not(:disabled) {
    transform: translateY(-1px);
    box-shadow: 0 8px 28px rgba(99,102,241,.45);
}
.submit-btn:active:not(:disabled) { transform: translateY(0); }
.submit-btn:disabled { opacity: .6; cursor: not-allowed; }

.btn-text, .btn-loader {
    display: flex; align-items: center; justify-content: center; gap: 8px;
}

.spin {
    animation: spin .8s linear infinite;
}
@keyframes spin {
    to { transform: rotate(360deg); }
}

/* Shake */
.shake-enter-active { animation: shake .4s; }
@keyframes shake {
    0%, 100% { transform: translateX(0); }
    20%       { transform: translateX(-5px); }
    40%       { transform: translateX(5px); }
    60%       { transform: translateX(-3px); }
    80%       { transform: translateX(3px); }
}

/* Footer */
.footer-text {
    margin-top: 24px;
    text-align: center;
    font-size: 11.5px;
    color: rgba(255,255,255,.18);
}
</style>