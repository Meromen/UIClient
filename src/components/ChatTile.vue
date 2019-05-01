<template>
    <div class="d-flex bd-highlight">
        <div class="img_cont">
            <img class="rounded-circle user_img">
            <span :class="{'online_icon': status, 'offline': !status}"></span>
        </div>
        <div class="user_info">
            <span>{{ name }}</span>
            <p>Bot is {{ status ? 'online' : 'offline' }} </p>
        </div>
    </div>
</template>

<script>
export default {
    data() {
        return {
            
        }
    },
    props: {
        name: {
            type: String,
            required: true
        },
        status: {
            type: Boolean,
            required: true
        },
        messages: {
            type: Array
        },
        messanger: {
            type: String
        }
    }
}
</script>
