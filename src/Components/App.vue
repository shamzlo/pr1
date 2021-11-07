<template>
    <div>
        <table class="table table-dark">
			<tr v-for="item in students" v-bind:key="item._id"> 
				<td v-if="item._id !== editStudentId">{{item.name}}</td>
                <td v-if="item._id !== editStudentId"><input type="checkbox" v-model="item.isDonePr"></td>
                <td v-if="item._id !== editStudentId">{{item.group}}</td>
                <td v-if="item._id !== editStudentId"><a href = "#" @click="deleteStudent(item._id)">Видалити</a></td>
                <td v-if="item._id !== editStudentId"><a href = "#" @click="setEditStudent(item)"><img width="30" height="30" v-bind:src="loadPen"></a></td>
                
                <td v-if="item._id === editStudentId"><input type="text" v-model="editState.name"></td>
                <td v-if="item._id === editStudentId"><input type="checkbox" v-model="editState.isDonePr"></td>
                <td v-if="item._id === editStudentId">
                    <select v-model="editState.group">
                        <option value="RPZ 17 1/9">RPZ 17 1/9</option>
                        <option value="RPZ 17 2/9">RPZ 17 2/9</option>
                    </select>
                </td>
                <td v-if="item._id === editStudentId"><button @click="editStudent(item)">EDIT</button></td>
			</tr>
		</table>

		<input v-model="addForm.name">
		<input type="checkbox" v-model="addForm.isDonePr">
		<select v-model="addForm.group">
			<option value="RPZ 18 1/9">RPZ 18 1/9</option>
			<option value="RPZ 18 2/9">RPZ 18 2/9</option>
		</select>
		<button @click="addStudent()">Add student</button>
    </div>
</template>

<script>
import Vue from 'vue'
import axios from 'axios'
import pen from '../img/pen.png'

export default {
    data() {
        return {
            students: [],
            search: '',
            piece: '',
            editStudentId: '',
            addForm: {
                group: '',
                isDonePr: false,
                name: '',
            },
            editState: {
                group: '',
                isDonePr: false,
                name: '',
            },
        }
    },
    mounted: function () {
        axios.get('http://46.101.212.195:3000/students').then((response) => this.students = response.data)
    },
    computed: {
        loadPen() {
            return pen
        }
    },
    methods: {
        deleteStudent(studId) {
            axios.delete("http://46.101.212.195:3000/students/" + studId).then(()=>{
                this.students = this.students.filter((element) => {
                    return element._id !== studId;
                });
            })
        },
        addStudent() {
            axios.post('http://46.101.212.195:3000/students', {
                name: this.addForm.name,
                group: this.addForm.group,
                isDonePr: this.addForm.isDonePr,
            }).then((response) => this.students.push(response.data))
        },
        setEditStudent(student) {
            this.editStudentId = student._id
            this.editState = { ...student }
            console.log(this.editStudentId, this.editState)
        },
        editStudent(student) {
            axios.put('http://46.101.212.195:3000/students/' + student._id, {
                name: this.editState.name,
                group: this.editState.group,
                isDonePr: this.editState.isDonePr,
            }).then((response) => {
                const { name, group, isDonePr } = response.data

                student.name = name
                student.group = group;
                student.isDonePr = isDonePr
                
                this.editStudentId = ''
            })
        }
    },
}
</script>