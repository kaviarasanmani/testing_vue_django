<template>
    <div class="page-my-account">
        <nav class="breadcrumb" aria-label="breadcrumbs">
            <ul>
                <li><router-link :to="{ name: 'Dashboard' }">Dashboard</router-link></li>
                <li class="is-active"><router-link :to="{ name: 'MyAccount' }" aria-current="true">MyAccount</router-link></li>
            </ul>
        </nav>
        <h1 class="title">My account</h1>

        <strong>Team: </strong>{{ team.name }}<br>
        <strong>Username: </strong>{{ $store.state.user.username }}<br>

        <hr>

        <div class="buttons">
            <router-link :to="{ name: 'EditTeam' }" class="button is-light">Edit team</router-link>
            <button @click="logout()" class="button is-danger">log out</button>
        </div>
    </div>
</template>

<script>
    import axios from 'axios'

    export default {
        name: 'MyAccount',
        data() {
            return {
                team: {}
            }
        },
        async mounted() {
            await this.getOrCreateTeam()
        },
        methods: {
            getOrCreateTeam() {
                axios
                    .get('/api/v1/teams/')
                    .then(response => {
                        this.team = response.data[0]
                        console.log(JSON.stringify(this.team.name))
                    })
                    .catch(error => {
                        console.log(JSON.stringify(error))
                    })
            },
            logout() {
                axios
                    .post("/api/v1/token/logout/")
                    .then(response => {

                        axios.defaults.headers.common["Authorization"] = ""

                        localStorage.removeItem("username")
                        localStorage.removeItem("userid")
                        localStorage.removeItem("token")

                        this.$store.commit('removeToken')

                        this.$router.push('/')
                    })
            }
        }
    }
</script>