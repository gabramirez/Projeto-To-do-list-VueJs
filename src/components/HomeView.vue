<template>
    <div class="containerGlobal">
        <div class="cardForm">
            <div class="titleContent">
                <h1 class="title">Board Tasks</h1>
            </div>
            <div class="inputContent">
                <input type="text" v-model="task" placeholder="Enter task" class="form-control mb-0" style="" />
                <button class="button" @click="submitTask">
                    <i class="fa-solid fa-plus" style="width: 1.5rem; border-radius: 5px;"></i>
                </button>
            </div>
            <div class="selectContent">
                 <div>
                    <h1 class="titleTable">Tasks created</h1>
                </div> 
                <div class="selectFilter">
                    <h1 for="" class="mb-0" style="font-size: 18px; font-family: Arial, Helvetica, sans-serif;">Filter:</h1>
                  <select v-model="filterStatus" name="selectFilter" class="form-control form-select">
                    <option value="">All</option>
                    <option v-for="status in statuses" :key="status" :value="status">{{ capitalizeFirstChar(status) }}</option>
                  </select>
                </div>
            </div>
            <!-- Task table -->
            <table class="table table-bordered">
                <tbody>
                    <tr v-for="(task, index) in filteredTasks" :key="index">
                        <td>
                            <span class="pointer noselect" style="cursor: pointer; font-weight: bold;"
                                @click="changeStatus(index)" :class="{
                                    'text-primary': task.status === 'To-do',
                                    'text-success': task.status === 'Done',
                                    'text-warning': task.status === 'In-progress',
                                }">
                                {{ capitalizeFirstChar(task.status) }}
                            </span>
                        </td>
                        <td>
                            <span class="text-break" :class="{ 'line-through': task.status === 'Done' }">
                                {{ task.name }}
                            </span>
                        </td>

                        <td class="text-center">
                            <div @click="deleteTask(index)">
                                <span class="far fa-trash-alt" style="cursor: pointer; color: red;"></span>
                            </div>
                        </td>
                        <td class="text-center">
                            <div @click="editTask(index)">
                                <p class="fa fa-pen pointer" style="cursor: pointer;"></p>
                            </div>
                        </td>
                    </tr>
                </tbody>
            </table>
        </div>
    </div>
</template>

<script>
import Swal from 'sweetalert2'
import 'bootstrap/dist/css/bootstrap.css'
import 'bootstrap/dist/js/bootstrap.js'
import '@fortawesome/fontawesome-free/css/all.css'

export default {
    name: 'HomeView',
    props: {
        msg: String,
    },
    data() {
        return {
            task: '',
            editedTask: null,
            statuses: ['To-do', 'In-progress', 'Done'],
            filterStatus: '',
            /* Status: 'To-do' / 'in-progress' / 'Done' */
            tasks: JSON.parse(localStorage.getItem('tasks')) || [],
        }
    },
    computed: {
        filteredTasks() {
            if (!this.filterStatus) {
                return this.tasks
            }
            return this.tasks.filter(task => task.status === this.filterStatus)
        },
    },
    methods: {
        /**
         * Capitalize first character
         */
        capitalizeFirstChar(str) {
            return str.charAt(0).toUpperCase() + str.slice(1);
        },
        /**
         * Change status of task by index
         */
        changeStatus(index) {
            let newIndex = this.statuses.indexOf(this.tasks[index].status);
            if (++newIndex > 2) newIndex = 0;
            this.tasks[index].status = this.statuses[newIndex];
            localStorage.setItem('tasks', JSON.stringify(this.tasks));
        },
        /**
         * Deletes task by index
         */
        deleteTask(index) {
            Swal.fire({
                title: 'Tem certeza?',
                text: 'Você tem certeza que deseja delatar esta tarefa?',
                icon: 'warning',
                showCancelButton: true,
                confirmButtonColor: '#3085d6',
                cancelButtonColor: '#d33',
                confirmButtonText: 'Sim, delatar!',
                cancelButtonText: 'Cancelar'
            }).then((result) => {
                if (result.isConfirmed) {
                   this.tasks.splice(index, 1);
                    localStorage.setItem('tasks', JSON.stringify(this.tasks));

                    Swal.fire({
                        title: 'Sucesso!',
                        text: 'Task delatada com sucesso!',
                        icon: 'success',
                        confirmButtonText: 'OK'
                    })
                }
            });
        },
        /**
         * Edit task
         */
        editTask(index) {
            this.task = this.tasks[index].name;
            this.editedTask = index;
        },
        /**
         * Add / Update task
         */
        submitTask() {
            if (this.task.length === 0) return;
                /* Edit */
            if (this.editedTask != null) {
                this.tasks[this.editedTask].name = this.task;
                this.editedTask = null;

                  Swal.fire({
                    title: 'Sucesso!',
                    text: 'Task editada com sucesso!',
                    icon: 'success',
                    confirmButtonText: 'OK'
                })
            } else {
                /* Create */
                this.tasks.push({
                    name: this.task,
                    status: "To-do",
                });
                   Swal.fire({
                    title: 'Sucesso!',
                    text: 'Task criada criada com sucesso!',
                    icon: 'success',
                    confirmButtonText: 'OK'
                })
            }
            this.task = "";
            localStorage.setItem('tasks', JSON.stringify(this.tasks));
        },
    },
};
</script>
<style lang="scss">
@import "@/scss/HomeView.scss";
</style>
