<template>
    <!-- HEADER -->

    <!-- BODY -->
    <body>
        <el-form>
            <el-form-item label="Clients Name: ">
                <el-input v-model="newClient.Name"/>
            </el-form-item>
            
            <el-form-item label="Clients Address: ">
                <el-input v-model="newAddress" maxlength="150" show-word-limit>
                    <template #append>
                        <el-button 
                            type="primary"
                            @click="AddNewAddress"
                        >
                                Add
                        </el-button>
                    </template>
                </el-input>
            </el-form-item>

            <el-form-item labe="Addresses List">
                <MainTable
                    :data="newClient.Addresses"
                    :columns="addressesColumns"
                    @handleDelete="hanldeDeletion"
                />
            </el-form-item>
            
            <el-form-item v-if="!isEdit" style="float: right">
                <el-button
                    type="primary"
                    @click="SaveNewClient"
                > 
                    Save 
                </el-button>
            </el-form-item>

            <el-form-item v-if="isEdit" style="float: right">
                <el-button
                    type="danger"
                    @click="SaveEditedClient"
                > 
                    Save 
                </el-button>
            </el-form-item>
        </el-form>
    </body>

    <!-- FOOTER -->
    <footer>
        
    </footer>
</template>

<script>
import axios from "axios";
import { ElNotification } from 'element-plus'
import MainTable from "./MainComponents/MainTable.vue"
export default {
    components: {
        MainTable
    },
    data(){
        return {
            //FORM DATA
            newAddress: "",
            newClient: {
                Name: "",
                Addresses: [],
            },
            
            //COLUMNS
            addressesColumns: [
                { prop: "id", label: "Id", width: 70 },
                { prop: "address", label: "Address", width: 450 },
                { prop: "Id", label: "", button: true, buttonName: "Delete", buttonType: "danger", width: 70}
            ],

            //EDITION
            isEdit: false
        }
    },
    methods: {
        AddNewAddress(){ 
            this.newClient.Addresses.push({
                id: this.newClient.Addresses.length > 0 ? this.newClient.Addresses[this.newClient.Addresses.length -1].id + 1 : 1,
                address: this.newAddress
            })
            this.newAddress = "";
        },
        SaveNewClient(){
            if (this.newClient.Name && this.newClient.Addresses.length > 0) {
                axios.post("https://localhost:44387/api/ClientCrud/CreateClient", {
                    clientName: this.newClient.Name,
                    clientAddresses: this.newClient.Addresses
                }).then(() => {
                    this.newClient.Name = "";
                    this.newClient.Addresses = [];
                    
                    this.newAddress = [];
                    this.$emit("SaveNewClient", this.newClient);
                });
            } else {
                ElNotification({
                    title: 'Error',
                    message: 'The new client must have a name and at least one address.',
                    type: 'error',
                    showClose: false
                })
            }

        },
        SaveEditedClient(){
            if (this.newClient.Name && this.newClient.Addresses.length > 0) {
                axios.post("https://localhost:44387/api/ClientCrud/EditClientOrAddresses", {
                    clientName: this.newClient.Name,
                    clientAddresses: this.newClient.Addresses
                }).then(() => {
                    this.newClient.Name = "";
                    this.newClient.Addresses = [];
                    
                    this.newAddress = [];
                    this.$emit("SaveNewClient", this.newClient);
                });
            } else {
                ElNotification({
                    title: 'Error',
                    message: 'The new client must have a name and at least one address.',
                    type: 'error',
                    showClose: false
                })
            }
        },
        hanldeDeletion(index, row){
            this.newClient.Addresses.splice(index, 1);
        },
        cleanForm() {
            this.isEdit = false;
            this.newAddress = "";
            this.newClient = {
                Name: "",
                Addresses: [],
            };
        }
    }
}
</script>

<style scoped>
body {
    background-color: white;
    padding: 20px;
}
</style>