<script>
    export default { 
        // Add code in appropriate places
        data() {
            return {
                items: ['keyboard', 'mouse', 'iPhone', 'macbook', 'adapter']
            };
        },
        methods:{
            delItem(index){
                this.items.splice(index,1)
            },
            addItem(){
                this.items.push(this.newItem)
                this.newItem="";
            }
        }
    };
</script>

<template>
<!-- TODO: Add Code Here -->
    <h2>Shopping Cart</h2>
    <ul>
    <li v-for="(item,index) in items" :key="index">{{ item }}
    <button @click="delItem(index)">Delete</button>
    </li>
        </ul>
<hr></hr>
<input v-model="newItem" /><button @click="addItem">Add</button>
</template>
