<template>
    <form id="form" action="#">
            <div className="function">
                <button @click='Them'>Thêm mới</button>
                <button @click='CapNhat'>Cập nhật</button>
                <button @click='XoaElements'>Xóa</button>
            </div>
            <div className="form_input">
                <label for='txtMaSV'><span :style="{color: 'red'}">*</span> Mã SV</label>
                <input type="text" id='txtMaSV' name='masv' v-model="sinhvien.masv" required /><br/>
                <label for='txtTenSV'><span :style="{color: 'red'}">*</span> Tên SV</label>
                <input type='text' id='txtTenSV' name='tensv' v-model="sinhvien.tensv" required/><br/>
                <label for='txtNgaySinh'>Ngày sinh</label>
                <input type='date' id='txtNgaySinh' name='ngaysinh' v-model="sinhvien.ngaysinhString"/><br/>

                <label htmlFor="rdbGioiTinh">Giới tính</label>
                <span id="rdbGioiTinh">
                    <input className="gt" type="radio" name="gioitinh" value="Nam" v-model="sinhvien.gioitinh" />
                    <label htmlFor="nam">Nam</label>
                    <input className="gt" type="radio" name="gioitinh" value="Nữ" v-model="sinhvien.gioitinh"/>
                    <label htmlFor="nu">Nữ</label>
                </span><br/> 
                
                <label htmlFor="drpKhoa"><span :style="{color: 'red'}">*</span> Khoa</label>
                <select id="drpKhoa" v-model="sinhvien.tenKhoa" required>
                    <option value=""></option>
                    <option v-for="op in dsKhoa" :key="op.id" :value="op.tenKhoa">
                        {{ op.tenKhoa }}
                      </option>
                </select>
            </div>
        </form>
</template>

<script>
    import {reactive, watch} from 'vue'
    export default {
        name:'Form-',
        setup(props,context) {

             const sinhvien = reactive({
                id: null,
                masv: '',
                tensv: '',
                ngaysinh: null,
                ngaysinhString: '',
                gioitinh: '',
                khoaid: null,
                tenKhoa: '',
            })


             watch(props.sinhVien, ()=>{
                if(props.isEdit && props.sinhVien.masv !== sinhvien.masv){
                    sinhvien.id = props.sinhVien.id;
                    sinhvien.masv = props.sinhVien.masv;
                    sinhvien.tensv = props.sinhVien.tensv;
                    sinhvien.ngaysinh = props.sinhVien.ngaysinh;
                    sinhvien.ngaysinhString = props.sinhVien.ngaysinhString;
                    sinhvien.khoaid = props.sinhVien.khoaid
                    sinhvien.gioitinh = props.sinhVien.gioitinh;
                    sinhvien.tenKhoa = props.sinhVien.tenKhoa;
                }
             })

            

            // watchEffect(()=>{                               // vì checked là 1 ref để quản lí sự thay đổi 'giá trị' của 1 ref dùng watchEffect nhưng nó sẽ render lần đầu tiên khi mounted conponent
            //     console.log(props.checked); 
            // }) 

            //hoặc                                          // hoặc

            // watch(()=>props.checked, (newValue)=>{       // dùng watch và tham số đầu tiên phải là 1 getter function/ 1 ref ' ()=>props.checked = toRef(props.checked, 'value') '
            //     console.log(newValue)                       // còn reactive thì chỉ cần truyền vào thôi vì 'giá trị' của reactive là reactive (hiểu đơn giản vậy chứ reactive object đã được Vue theo dõi và tự động theo dõi sự thay đổi của các thuộc tính bên trong nó )
            // })                                              // render khi nào giá trị thay đổi


            // watch(()=>props.checked, (newValue)=>{
            //     cked.value = newValue.value
                
            // })
            
            function XoaElements(e){
                e.preventDefault()
                
                props.DeleteItems()
                
            }

            function CapNhat(e){
                e.preventDefault();
                props.update(sinhvien)
                reSetSinhVien()
            }


            function Them(e){
                e.preventDefault();
                if(sinhvien.masv !== '' && sinhvien.tensv !== '' && sinhvien.khoaid !== ''){
                    const copySV = reactive({...sinhvien})
                    context.emit('add', copySV);
                    reSetSinhVien()    
                }else{
                    alert('Các field * không được trống!')
                }                       
            }

            function reSetSinhVien(){
                sinhvien.id = null
                sinhvien.masv =  '',
                sinhvien.tensv = '',
                sinhvien.ngaysinh = null,
                sinhvien.ngaysinhString = ''
                sinhvien.gioitinh = ''
                sinhvien.khoaid = null
                sinhvien.tenKhoa = ''
            }
            
            return {
                sinhvien,
                Them,
                CapNhat,
                XoaElements,
            }
        },
        emits: ['add', 'delete'],
        props:{
            sinhVien: Object,
            danhSach: Array,
            update: Function,
            checked:Array,
            isEdit: Boolean,
            DeleteItems: Function,
            dsKhoa: Array
        }
    }
</script>

<style lang="scss" scoped>

</style>