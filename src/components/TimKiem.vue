<template>
    <div className="search">
        <label for="txtTuKhoa">Từ khóa</label>
        <input type="text" placeholder="Từ khóa cần tìm" v-model="word"/>
        <button @click="TimKiem">Tìm kiếm</button>
    </div>
</template>

<script>
import { ref } from 'vue'

    export default {
        name:'TimKiem',
        setup(props,context) {
            
            const word = ref('')
           
            function TimKiem(){
                // props.finding(word)
                context.emit('search', word)
            }

           
            return {
                TimKiem,word
            }
        },
        emits:['search'],
        props:{
            finding: Function,
            modelValue: String
        }
    }
</script>

<style lang="scss" scoped>

</style>