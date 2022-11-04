<template>
    <div class="header">
        <!-- Display as default, or when search is not active -->
        <div v-if="!searchActive">
            <nav class="header__navigation">
                <button class="header__info" @click="toggleAbout">
                        <img src="/images/InfoCircle.svg" alt="open info page">
                </button>

                <div class="header__location" @click="isSearchActive()">
                    <h1>{{modelValue}}</h1>
                </div>

                <button class="header__search">
                    <img src="/images/Search.svg" alt="open search field" @click="isSearchActive()">
                </button>
            </nav>
        </div>
        <!-- If searchbutton has been clicked, show this -->
        <div v-else>
            <nav class="header__navigation">
                <!-- <button class="header__info" @click="toggleAbout">
                    <img src="/images/InfoCircle.svg" alt="open info page">
                </button> -->
                <div>

                </div>

                <!-- <input type="text" ref="input" v-model="searchField" @keyup.enter="changeCity(searchField), isSearchActive()"> -->

                <input type="text" ref="input" :value="modelValue" @input="$emit('update:modelValue', $event.target.value)" @keyup.enter="changeCity(), isSearchActive()">

                <button class="header__search" @click="changeCity(), isSearchActive()">
                    <img src="/images/Search.svg" alt="search for city">
                </button>
            </nav>
        </div>
    </div>
</template>

<script>

export default {
    props: ['modelValue'],
    emits: ['update:modelValue', 'changeCity'],
    data() {
        return {
            searchActive: false,
            searchField: '',
        }
    },
    methods: {
        toggleAbout() {
            this.$router.push({name: 'about'})
        },
        isSearchActive() {
            if (this.searchActive === false) {
                this.searchActive = true
                setTimeout(this.selectText, 100)
            } else {
                this.searchActive = false
            }
        },
        selectText() {
            this.$refs.input.select()
        },
        changeCity() {
            this.$emit('changeCity')
            console.log('changecity ran in header')
        }
    }
}
</script>

<style>
    .header {
        margin: 0 auto;
        width: 100%;
        max-width: var(--max-width-global);
    }

    .header img {
        height: var(--icon-size-medium);
        min-width: 20px;
    }

    .header__navigation {
        height: 3.5rem;
        display: grid;
        grid-template-columns: 1fr auto 1fr;
        align-items: center;
        /* text-transform: capitalize; */
    }

    .header__location{
        max-width: 80%;
        max-height: 1em;
    }

    .header__location h1 {
        font-size: 1.5rem;
    }

    .header__navigation input {
        background: white;
        padding: 0.5rem;
        border-radius: 3px;
    }

    .header__info {
        justify-self: start;
    }

    .header__search {
        justify-self: end;
    }
</style>   