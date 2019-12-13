<template>
    <div class="overlay">
        <!-- Agent Icon -->
        <img v-if="app.avatarUri" class="app-icon" :alt="app.displayName" src="https://lh3.googleusercontent.com/-SOWejb8NmPc/AAAAAAAAAAI/AAAAAAAAAAA/ACHi3rdG9SQGg2HVUujcXKccMlnUcMsmgg/photo.jpg">
        <img v-else class="app-icon" src="https://console.dialogflow.com/api-client/assets/img/logo-short.png" :alt="app.displayName">

        <!-- Agent Title -->
        <h1 class="app-title">{{(config.i18n[sel_lang] && config.i18n[sel_lang].welcomeTitle) || config.i18n[config.app.fallback_lang].welcomeTitle}} {{app.displayName}}</h1>

        <!-- Agent Description -->
        <p class="app-description">{{app.description}}</p>
        <p class="app-description"><b><i>Hi</i></b> there! I am <u>ProdigyBot</u> and very simple to use. You can look for a home to <b><i>rent</i></b> or <b><i>buy</i></b> and even <b><i>sell</i></b> or <b><i>lease</i></b>. All you have to do is write down what you want and answer the questions.  I can also help you by answering a number of frequently asked questions (<b><i>faq</i></b>). You can <b><i>cancel</i></b> at any moment and start fresh.<br>
        Feel free to ask for <b><i>help</i></b>, you are human after all :(</p>

        <!-- Language picker, when your Agent supports more than one Language -->
        <div v-if="app.supportedLanguageCodes && app.supportedLanguageCodes.length > 0">
            <button class="language-picker" role="checkbox" :class="{'picked': sel_lang == app.defaultLanguageCode}" @click="sel_lang = app.defaultLanguageCode">
                {{app.defaultLanguageCode | toLang}}
            </button>
            <button
                v-for="language in app.supportedLanguageCodes"
                :key="language"
                class="language-picker"
                role="checkbox"
                :class="{'picked': sel_lang == language}"
                @click="sel_lang = language">
                {{language | toLang}}
            </button>
        </div>
    </div>
</template>

<style lang="sass" scoped>
@import './../App/Mixins.sass'

.overlay
    text-align: center
    padding-top: 25px

.app-icon
    width: 120px
    height: 120px
    border-radius: 16px
    object-fit: cover
    background-color: var(--image-background)

.app-title
    font-weight: 500
    font-size: 24px
    margin-top: 30px
    color: var(--text)
    line-height: 20px

.app-description
    font-size: 16px
    color: var(--text-secondary)

.language-picker
    @include reset
    display: inline-block
    border: 1px solid var(--border)
    padding: 8px 12px
    border-radius: 40px
    cursor: pointer
    font-weight: 500
    margin-right: 2px
    color: var(--text)

    &.picked
        background-color: var(--element-background)
        border: 1px solid var(--element-background)
</style>

<script>
const langs = require('langs')

export default {
    name: 'Welcome',
    filters: {
        /* This filter turns language code to the local language name using the langs dependency (example "en" -> "English") */
        toLang(code){
            return langs.where('1', code).local
        }
    },
    props: {
        app: {
            type: Object,
            required: true
        }
    },
    data(){
        return {
            sel_lang: ''
        }
    },
    watch: {
        /* Save selected language */
        sel_lang(lang){
            if (this.history()) localStorage.setItem('lang', lang)

            else {
                this.config.app.fallback_lang = lang
            }
        }
    },
    /* Set default language on load (or fallback) */
    created(){
        if (this.app && this.app.defaultLanguageCode){
            this.sel_lang = this.app.defaultLanguageCode
        }

        else {
            this.sel_lang = this.config.app.fallback_lang
        }
    }
}
</script>