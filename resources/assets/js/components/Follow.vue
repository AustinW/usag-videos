<template>
    <div>
        <div v-if="subjectId !== userId">
            <!--Not followed-->
            <button :class="styles" v-if="followed === 0" @click="follow">
                <i class="glyphicon glyphicon-eye-open"></i> Follow
            </button>

            <!--Needs verification-->
            <button :class="styles" v-if="followed === 1" disabled>
                <i class="glyphicon glyphicon-hourglass"></i> Waiting for verification
            </button>

            <!--Verified-->
            <button :class="styles" v-if="followed === 2" @click="unfollow">
                <i class="glyphicon glyphicon-eye-close"></i> Unfollow
            </button>
        </div>
    </div>
</template>

<script>
    export default {
        data() {
            return {
                followed: null,
            }
        },

        props: {
            subjectId: {
                required: true,
                type: Number
            },
            userId: {
                required: true,
                type: Number
            },
            isFollowed: {
                type: Number
            },
            styles: {
                required: false,
                type: String,
                default: 'btn btn-default'
            }
        },

        methods: {
            follow() {
                this.$http.post('/athletes/follow', {
                    subjectId: this.subjectId
                }).then(Vue.getJson).then((response) => {
                    if (response.status == "ok") {
                        this.followed = (response.verified == true) ? 2 : 1;
                    }
                })
            },

            unfollow() {
                this.$http.post('/athletes/unfollow', {
                    subjectId: this.subjectId
                }).then(Vue.getJson).then((response) => {
                    if (response.status == "ok") {
                        this.followed = 0;
                    }
                })
            }
        },

        mounted() {
            if (!this.isFollowed && this.userId) {
                this.$http.get('/athletes/check-follow/' + this.subjectId).then(Vue.getJson).then((response) => {
                    this.followed = response.followCode;
                });
            } else {
                this.followed = parseInt(this.isFollowed);
            }

        }
    };
</script>