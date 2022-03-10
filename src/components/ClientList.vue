<template>
    <!-- HEADER -->
    <!-- <header>
        <h1>Client List Main Page</h1>
    </header> -->

    <!-- BODY -->
    <body>
        <el-container style="height: 500px">
            <el-header style="background-color: #B2CDCD">
                <h1 style="color: white; margin: 10px 50%; width: 200px">Client List</h1>
            </el-header>
            <el-container>
                <el-aside width="400px" style="background-color: white">
                    
                    <el-button
                        type="primary"
                        style="margin: 10px; float: right"
                        size="small"
                        @click="AddNewClient"
                    >
                        New 
                    </el-button>
                    
                    <MainTable
                        :data="customerList"
                        :columns="customerColumns"
                        @selectedRow="handleSelection"
                        @handleEdit="handleEdition"
                        @handleButtonConfirmation="handleDeletion"

                    />
                </el-aside>
                <el-container>
                <el-main style="background-color: #e0ebeb">
                    <MainTable
                        :data="addressesFilteredList"
                        :columns="addressesColumns"
                    />
                </el-main>
                <el-footer style="background-color: #B2CDCD">Footer</el-footer>
                </el-container>
            </el-container>
        </el-container>

        <el-dialog
            v-model="showDialog"
            :title="isEdit ? 'Edit Client' : 'Create new Client'"
            :beforeClose="handleCloseDialog">
                <NewClientForm ref="newClientForm" @SaveNewClient="handleSaveNewCliente"/>
        </el-dialog>
    </body>

    <!-- FOOTER -->
    <footer>
        
    </footer>
    
</template>

<script>
import axios from "axios";
import MainTable from "./MainComponents/MainTable.vue";
import NewClientForm from "./NewClientForm.vue";

export default {
    components: {
        MainTable,
        NewClientForm
    },
    data(){
        return{
            //Customer
            customerList: [],
            customerColumns: [
                { prop: "id", label: "Id", width: 70},
                { prop: "name", label: "Client Name", width: 200 },
                { prop: "id", label: "", button: true, buttonType: "danger", buttonName: "Delete", popConfirm: true, width: 70},
                { prop: "id", label: "", button: true, buttonType: "warning", buttonName: "Edit", width: 70 }
            ],

            //Addresses
            addressesList: [],
            addressesFilteredList: [],
            addressesColumns: [
                { prop: "id", label: "Id", width: 70},
                { prop: "address", label: "Address"},
            ],

            //Dialog
            showDialog: false,
            isEdit: false
        }
    },
    mounted() {
        this.getClientsList();
    },
    methods: {
        AddNewClient(){
            this.showDialog = true;
        },
        handleCloseDialog(){ 
            this.showDialog = false;
            this.$refs.newClientForm.cleanForm();
        },
        handleSaveNewCliente(){
            this.showDialog = false;
            this.getClientsList();
            this.addressesFilteredList = [];
        },

        //API Calls
        getClientsList(){
            axios.get("https://localhost:44387/api/ClientCrud/GetClients")
            .then(resp => {
                this.customerList = resp.data;
            });
        },
        handleSelection(selectedRow){
            axios.get(`https://localhost:44387/api/ClientCrud/GetClientAddresses/${selectedRow.id}`)
            .then(resp => {
                this.addressesFilteredList = resp.data;
            })
        },
        handleDeletion(index, selectedRow){
            axios.delete(`https://localhost:44387/api/ClientCrud/DeleteClient/${selectedRow.id}`)
            .then(() => {
                this.addressesFilteredList = [];
                this.getClientsList();
            });
        },
        handleEdition(index, selectedRow){
            this.isEdit = true;
            this.showDialog = true;
            this.$refs.newClientForm.isEdit = true;
            this.$refs.newClientForm.newClient.Name = selectedRow.name;
            this.$refs.newClientForm.newClient.Addresses = this.addressesFilteredList;
            console.log("INDEX: ", index, "SELECTED ROW: ", selectedRow);
        }
    }
}
</script>

<style>

.el-dialog{
    background-color: #e0ebeb;
}

</style>