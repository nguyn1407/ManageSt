<template>
    <div>
        <h1>DANH SÁCH SINH VIÊN</h1>
        <TimKiem @search="search" :word="word" ></TimKiem>
        <DanhSach :danhSach="danhSach" :danhSachSearch="danhSachSearch" @edit="Edit" @xoa="Xoa" :selected="selected" @check="check" @uncheck="uncheck" :word="word" :dsKhoa="dsKhoa"></DanhSach>
        <div class="paginate" style="margin-top: 20px;">
            <div class="page-left">
                <span>Per Page: </span>
                <span>
                    <select v-model="page.PageSize" @change="changePerPage()">
                        <option value="2">2</option>
                        <option value="5">5</option>
                        <option value="10">10</option>
                    </select>
                </span>
            </div>
            <div class="page-right">
                <paginate
                    v-model="page.PageNumber"
                    :page-count="page.TotalPage"
                    :page-range="3"
                    :margin-pages="2"
                    :click-handler="clickCallback"
                    :prev-text="'<<'"
                    :next-text="'>>'"
                    :container-class="'pagination'"
                    :page-class="'page-item'"
                >
                </paginate>
                <span>Tổng : {{page.TotalCount}} bản ghi</span>
            </div>
        </div>
        <Form @add="add" :sinhVien="sinhVien" :danhSach="danhSach" :update="Update" :isEdit="isEdit" :DeleteItems="DeleteItems" :dsKhoa="dsKhoa"></Form>
    </div>
</template>

<script>
import { onMounted, reactive, ref, watch} from 'vue'
import DanhSach from './DanhSach.vue'
import Form from './Form.vue'
import TimKiem from './TimKiem.vue'
import moment from "moment";
import Paginate from "vuejs-paginate-next";

    export default {
        name:'QLDSSV',
        components:{
            TimKiem,
            DanhSach,
            Form,
            Paginate
        },
        setup() {

            const danhSach = ref([])

            const dsKhoa = ref([])

            const sinhVien = reactive({})

            const isEdit = ref(false)

            const selected = ref([])

            const word = ref('')

            const danhSachSearch = ref([])

            const change = ref(false)

            const page = reactive({
                PageNumber: 1,
                PageSize: 2,
                TotalPage: 0,
                TotalCount: 0
            })


            onMounted(()=>{
                getAll();

                fetch("https://localhost:7260/GetAllKhoas")
                .then(response => {
                    if (!response.ok) throw Error(response.statusText);
                    return response.json();
                })
                .then(data => dsKhoa.value = data.data)
                .catch(error => {
                    console.log("Somethings went wrong!!", error);
                });
            })

            function getAll(){
                fetch(`https://localhost:7260/api/SinhVien/GetAllSinhViens?PageNumber=${page.PageNumber}&PageSize=${page.PageSize}`)
                .then(response => {
                    if (!response.ok) throw Error(response.statusText);
                    return response.json();
                })
                .then(json => {
                    danhSach.value = json.data.data
                    page.TotalPage = json.data.pagination.totalPages
                    page.TotalCount = json.data.pagination.totalCount
                })
                .catch(error => {
                    console.log("Somethings went wrong!!", error);
                });
            }

            function clickCallback (pageNum){
                page.PageNumber = pageNum
                if(word.value === '' || word.value === null){
                    getAll()
                }
                else{
                    getAllSearch()
                }
                    
            }

            function changePerPage(){
                page.PageNumber = 1
                if(word.value === '' || word.value  === null){
                    getAll()
                }
                else{
                    getAllSearch()
                }
            }


             // cập nhật lại dữ liệu khi component render lại khi có dữ liệu setter của watch thay đổi (theo dõi xem setter thay đổi thì làm gì đó)
             // đoạn này dùng 1 computed có khi hợp lí hơn (không cần tạo 2 cái watch) vì chỉ cần danhSach thay đổi thì tính toán lại (filter dữ liệu) rồi để danhSachSearch hứng lại
            watch(() => danhSach.value.length, ()=>{
                danhSachSearch.value = danhSach.value.filter(ele => ele.masv.toLowerCase().includes(word.value.toLowerCase()) || ele.tensv.toLowerCase().includes(word.value.toLowerCase()) || ele.gioitinh.toLowerCase().includes(word.value.toLowerCase())) 
            },{immediate:true})

            watch(() => danhSach.value, ()=>{
                danhSachSearch.value = danhSach.value.filter(ele => ele.masv.toLowerCase().includes(word.value.toLowerCase()) || ele.tensv.toLowerCase().includes(word.value.toLowerCase()) || ele.gioitinh.toLowerCase().includes(word.value.toLowerCase())) 
            },{immediate:true})


            // watch(() => word.value, () => {
            //     changePerPage()
            // })



            function add(sinhvien){
                const addSV = danhSach.value.find(sv => sv.masv === sinhvien.masv)
                console.log(addSV);
                if(addSV !== undefined){
                    alert('Mã sv đã tồn tại sv')
                    console.log(addSV);
                }else{
                    change.value = false;

                    sinhvien.khoaid = dsKhoa.value.find(khoa => khoa.tenKhoa === sinhvien.tenKhoa).id

                    fetch("https://localhost:7260/api/SinhVien/CreateNew", {
                        method: "POST",
                        headers: {
                            "Content-Type": "application/json",
                        },
                        body: JSON.stringify(sinhvien)
                    })
                    .then(res => res.json())
                    .then(value => {
                        console.log('add', value);
                        if(value.status){
                            if(word.value === null || word.value === ''){
                                getAll()
                            }else{
                                getAllSearch()
                            }
                        }else{
                            console.log(value.message);
                        }
                    })
                
                }
            }


            function Edit(masv){
                let sv;
                if(word.value !== '' || word.value !== null){
                     sv =  danhSachSearch.value.find(value => value.masv === masv)
                }else{
                     sv =  danhSach.value.find(value => value.masv === masv)
                }
                sinhVien.id = sv.id
                sinhVien.masv = sv.masv;
                sinhVien.tensv = sv.tensv;
                sinhVien.ngaysinh = moment(sv.ngaysinhString).format("YYYY-MM-DD");
                sinhVien.ngaysinhString = sv.ngaysinhString
                sinhVien.gioitinh = sv.gioitinh;
                sinhVien.khoaid = sv.khoaid;
                sinhVien.tenKhoa = sv.tenKhoa;
                isEdit.value = true
                document.getElementById("txtMaSV").setAttribute("disabled", true);
            }

            function Update(sv){
                 if(sv.masv === ''){
                    alert("Chọn sinh viên cần sửa!")
                 }else{
                    sv.khoaid = dsKhoa.value.find(khoa => khoa.tenKhoa === sv.tenKhoa).id

                    fetch(`https://localhost:7260/api/SinhVien/UpdateSinhVien/${sv.masv}`, {
                        method: "PUT",
                        headers: {
                            "Content-Type": "application/json",
                        },
                        body: JSON.stringify(sv)
                    })
                    .then(res => res.json())
                    .then(value => {
                        console.log('update', value)
                        if(value.status){
                            if(word.value === null || word.value === ''){
                                getAll()
                            }else{
                                getAllSearch()
                            }
                        }else{
                            console.log("Can't update");
                        }
                    })

                    isEdit.value = false
                    sinhVien.id = null;
                    sinhVien.masv = '';
                    sinhVien.tensv = '';
                    sinhVien.ngaysinh = null;
                    sinhVien.ngaysinhString = ''
                    sinhVien.gioitinh = '';
                    sinhVien.khoaid = null;
                    sinhVien.tenKhoa ='';
                    document.getElementById("txtMaSV").removeAttribute("disabled");
                 }
            }


            function Xoa(masv){
                fetch(`https://localhost:7260/api/SinhVien/DeleteOne/${masv}`, {
                    method: "DELETE",
                    headers: {
                        "Content-Type": "application/json",
                    },
                })
                .then(res => res.json())
                .then(value => {
                    console.log('delete', value)
                    if(word.value === null || word.value === ''){
                        getAll()
                    }else{
                        getAllSearch()
                    }
                })
            }

           function check(checked){
                selected.value = checked.value
                
           }

            function DeleteItems(){       
                if(selected.value.length === 0){
                    alert('Chọn sinh viên cần xóa!!')
                }else{
                    if(window.confirm('Are you sure?')){
                        fetch("https://localhost:7260/api/SinhVien/DeleteSinhViens", {
                        method: "DELETE",
                        headers: {
                            "Content-Type": "application/json",
                        },
                        body: JSON.stringify(selected.value)
                        })
                        .then(res => res.json())
                        .then(value => {
                            console.log('deletes', value)
                            if(word.value === null || word.value === ''){
                                getAll()
                            }else{
                                getAllSearch()
                            }
                        })

                        selected.value = []
                    }
                }
            }

            function search(search){
                 word.value = search.value
                 page.PageNumber = 1
                if(search.value === null || search.value === ''){
                    alert('Chưa nhập từ cần tìm')
                    getAll()
                }else{
                    getAllSearch()
                    
                }
            }

            function getAllSearch(){
                fetch(`https://localhost:7260/api/SinhVien/SearchSV/${word.value}?PageNumber=${page.PageNumber}&PageSize=${page.PageSize}`)
                    .then(response => {
                        if (!response.ok) throw Error(response.statusText);
                        return response.json();
                    })
                    .then(json => {
                        danhSachSearch.value = json.data.data
                        page.TotalPage = json.data.pagination.totalPages
                        page.TotalCount = json.data.pagination.totalCount
                        // getAll()
                    })
                    .catch(error => {
                        console.log("Somethings went wrong!!", error);
                    });
            }

            

            return {
                danhSach,
                dsKhoa,
                add,
                Edit,
                sinhVien,
                Update,
                Xoa,
                DeleteItems,
                search,
                isEdit,
                selected,
                check,      
                word,
                danhSachSearch,
                getAll, 
                page,
                clickCallback,
                changePerPage,
                getAllSearch
            }
        },
    }
</script>

<style lang="css" scoped>
    @import "https://cdn.jsdelivr.net/npm/bootstrap@5.1.3/dist/css/bootstrap.min.css";

    .paginate{
        width: 1000px;
        display: flex;
        justify-content: space-between;
        
    }

    .page-right{
        
        width: 420px;
    }
</style>