<template>
    <el-table
        :data="data"
        highlight-current-row
        @current-change="handleCurrentChange" 
        style="width: 100%"
    >
        <el-table-column
            v-for="(column, key) in columns"
            :key="key" 
            :label="column.label"
            :prop="column.prop"
            :width="column.width"
            :resizable="true"
        >
            <template #default="scope" v-if="column.button">
                <el-popconfirm
                    v-if="column.popConfirm"
                    title="Are you sure to delete this client?"
                    @confirm="handleConfirmation(scope.$index, scope.row)"
                    @cancel="handleCancellation(scope.$index, scope.row)">
                    <template #reference>
                        <el-button
                        size="small"
                        :type="column.buttonType"
                        @click="handleButtonAction(scope.$index, scope.row, column.buttonName)"
                        >{{column.buttonName}}</el-button>
                    </template>
                </el-popconfirm>

                 <el-button
                        v-else
                        size="small"
                        :type="column.buttonType"
                        @click="handleButtonAction(scope.$index, scope.row, column.buttonName)"
                        >{{column.buttonName}}</el-button>

            </template>
            
        </el-table-column>
    </el-table>
</template>

<script>
export default {
    props: {
        data: {
            type: Array
        },
        columns: {
            type: Array
        },
        highlightRow: {
            type: Boolean,
            default: false
        }
    },
    methods: {
        handleCurrentChange(row){
            this.$emit("selectedRow", row);
        },
        handleButtonAction(index, row, buttonName){
            this.$emit(`handle${buttonName}`, index, row);
        },
        handleConfirmation(index, row){
            this.$emit("handleButtonConfirmation", index, row);
        },
        handleCancellation(index, row){
            this.$emit("handleButtonCancellation", index, row);
        }
    }

}
</script>

<style>

</style>