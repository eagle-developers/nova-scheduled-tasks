<template>
    <card class="h-auto p-4">
        <h2 class="text-90 font-light mb-4">Scheduled Tasks</h2>

        <p v-if="loaded && ! tasks.length">You do not currently have any scheduled tasks.</p>

        <loader v-if="! loaded" class="mb-4"></loader>

        <table v-if="loaded && tasks.length" class="table w-full">
            <thead>
                <tr>
                    <th class="text-left">Command</th>
                    <th class="text-left">Expression</th>
                    <th class="text-left">Next Run At</th>
                </tr>
            </thead>
            <tbody>
                <tr v-for="(task, index) in tasks" :task="task">
                    <td>{{ task.command }}</td>
                    <td>{{ task.expression }}</td>
                    <td>{{ formatNextRunAt(task.nextRunAt) }}</td>
                </tr>
            </tbody>
        </table>

    </card>
</template>

<script>
    import formatters from '../mixins/formatters'

    export default {
        mixins: [formatters],

        props: ['card'],

        data: () => {
            return {
                limit: 5,
                loaded: false,
                tasks: [],
            }
        },

        mounted() {
            this.fetchTasks()
        },

        methods: {
            fetchTasks() {
                Nova.request().get('/nova-vendor/eagle-developers/nova-scheduled-tasks/tasks?limit=' + this.limit).then((response) => {
                    this.tasks = response.data
                    this.loaded = true

                    setTimeout(this.fetchTasks, 60 * 1000)
                })
            },
        }
    }
</script>
