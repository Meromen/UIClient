<template>
    <div class="d-flex mb-4" :class="{'justify-content-start': sender, 'justify-content-end': !sender}">
        <div class="img_cont_msg">
            <img class="rounded-circle user_img_msg">
        </div>
        <div :class="{'msg_cotainer': sender, 'msg_cotainer_send': !sender}">
            {{ message }}
            <span class="msg_time">8:40 AM, Today</span>
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
        message: {
            type: String,
            required: true
        },
        sender: {
            type: Boolean,
            required: true
        }
    }
}
</script>