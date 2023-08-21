<template>
    <div>
        <table id="results" >
                <thead> 
                <tr>  
                    <th></th>
                    <th>Mã sv</th>
                    <th>Tên sv</th>
                    <th>Ngày sinh</th>
                    <th>Giới tính</th>
                    <th>Khoa</th>
                    <th>Thao tác</th>
                </tr>
                </thead>
                <tbody v-if="word">
                    <tr v-for="(ele, index) in danhSachSearch " :key="index">
                        <td><input type="checkbox" :value="ele.masv" v-model="checked"/></td>
                        <td>{{ele.masv}}</td>
                        <td>{{ele.tensv}}</td>
                        <td>{{ele.ngaysinhString}}</td>
                        <td>{{ele.gioitinh}}</td>
                        <td>{{ele.tenKhoa}}</td>
                        <td>
                            <button @click='Sua(ele.masv)' href="#"><i className="fa-solid fa-pen-to-square"></i> Sửa</button>
                            <button @click='Xoa(ele.masv)' href="#"><i className="fa-solid fa-trash"></i> Xóa</button>
                        </td>
                    </tr>
                </tbody>
                <tbody v-else>
                    <tr v-for="(ele, index) in danhSach" :key="index">
                        <td><input type="checkbox" :value="ele.masv" v-model="checked"/></td>
                        <td>{{ele.masv}}</td>
                        <td>{{ele.tensv}}</td>
                        <td>{{ele.ngaysinhString}}</td>
                        <td>{{ele.gioitinh}}</td>
                        <td>{{ele.tenKhoa}}</td>
                        <td>
                            <button @click='Sua(ele.masv)' href="#"><i className="fa-solid fa-pen-to-square"></i> Sửa</button>
                            <button @click='Xoa(ele.masv)' href="#"><i className="fa-solid fa-trash"></i> Xóa</button>
                        </td>
                    </tr>
                </tbody>
        </table>
    </div>
</template>

<script>
import { ref, watch} from 'vue'

    export default {
        name:'DanhSach',
        setup(props, context) {

            const data = ref('')

            context.emit('saveData', data)


            function Sua(masv){
                context.emit('edit', masv)
            }

            function Xoa(masv){
                if(window.confirm('Are you sure?')){
                    context.emit('xoa', masv)
                }
            }

            const checked = ref([])
            watch(checked, ()=>{
                context.emit('check', checked)
            })


            watch(()=>props.selected.length, ()=>{
                if(props.selected.length === 0){
                    checked.value = [];
                }
            }, {immediate:true})
            
            return {
                Sua, Xoa, checked, data, 
            }
        },
        props:{
            danhSach:Array,
            selected: Array,
            word: String,
            danhSachSearch: Array,
            dsKhoa: Array
        },
        emits:['edit', 'xoa', 'check', 'uncheck', 'saveData']
    }
</script>

<style scoped>
    #results{
        border: 1px solid black;
    }
</style>