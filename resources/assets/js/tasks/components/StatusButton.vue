<template>
    <transition mode="out-in">
        <button v-if="task.activated" type="button" class="uk-button uk-button-small" :class="{ 'uk-button-primary': !hovering && !working, 'uk-button-danger': hovering && !working, 'uk-spinner uk-icon uk-button-secondary': working}" key="enabled" @mouseenter="hovering = true" @mouseleave="hovering = false" @click="deactivate" :disabled="working">
            <span v-if="!working">{{ activeStatusText }}</span>
            <icon v-if="working" name="spinner" :spin="true" :scale="80"></icon>
        </button>
        <button v-if="existsAndIsInActive" type="button" class="uk-button uk-button-small" :class="{'uk-button-danger': !hovering && !working, 'uk-button-primary': hovering && !working, 'uk-spinner uk-icon uk-button-secondary': working}" key="disabled" @mouseenter="hovering = true" @mouseleave="hovering = false" @click="activate" :disabled="working">
            <span v-if="!working">{{ inActiveStatusText }}</span>
            <icon v-if="working" name="spinner" :spin="true" :scale="80"></icon>
        </button>
    </transition>
</template>

<script>
    import Icon from '../../components/Icon.vue'
    export default {
        components: {
            'icon':Icon
        },
        props: [
            'dataTask',
            'dataExists'
        ],
        data() {
            return {
                hovering: false,
                working: false,
                task: this.dataTask,
                exists: this.dataExists,
            }
        },
        computed: {
            inActiveStatusText() {
                return this.hovering ? 'Enable' : 'Disabled'
            },
            activeStatusText() {
                return this.hovering ? 'Disable' : 'Enabled'
            },
            existsAndIsInActive() {
                return !this.task.activated && this.exists;
            }
        },
        methods: {
            activate() {
                this.working = true

                axios.post('/totem/tasks/status', { task_id: this.task.id })
                    .takeAtLeast(500)
                    .then(response => {
                        this.task = response.data
                        this.working = false
                        this.hovering = false
                    })
            },
            deactivate() {
                this.working = true

                axios.delete(`/totem/tasks/status/${this.task.id}`)
                    .takeAtLeast(500)
                    .then(response => {
                        this.task = response.data
                        this.working = false
                        this.hovering = false
                    })
            },
        },
        mounted() {
        }
    }
</script>
