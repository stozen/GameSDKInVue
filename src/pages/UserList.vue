<template>
    <div>
        <el-dropdown>
            <el-button type="primary">
                {{gameName}}
                <i class="el-icon-arrow-down el-icon--right"></i>
            </el-button>
            <el-dropdown-menu slot="dropdown">
                <el-dropdown-item v-for="(item,index) in items" :key="index" @click.native="setGameName(item.gameName,item.gid)">{{item.gameName}}</el-dropdown-item>
            </el-dropdown-menu>
        </el-dropdown>

        <el-table v-loading="isLoading" border :data="tableData" height="770" class="table">
            <el-table-column prop="uid" label="id" width="100">
            </el-table-column>
            <el-table-column prop="userName" label="用户名" width="150">
            </el-table-column>
            <el-table-column prop="userImg" label="头像" align="center" width="75">
                <template slot-scope="scope">
                    <img class="user-img" :src="scope.row.userImg" />
                </template>
            </el-table-column>
            <el-table-column prop="userToken" label="token" width="300">
            </el-table-column>
            <el-table-column prop="userLevel" label="等级" width="100">
            </el-table-column>
            <el-table-column prop="userSex" label="性别" width="100">
            </el-table-column>
            <el-table-column prop="userOpenId" label="openId" width="300">
            </el-table-column>
            <el-table-column prop="platform" label="平台" width="100">
            </el-table-column>
            <el-table-column prop="createTime" label="创建时间" width="250">
            </el-table-column>
        </el-table>
    <el-pagination class="pagination" background layout="prev, pager, next" :page-size="10" @current-change="handleCurrentChange"  :current-page="currentPage" :total="allPage"/>
    </div>
</template>

<script>
export default {
    data() {
        return {
            tableData: [],
            gameName: "全部游戏",
            gameId: 0,
            isLoading: false,
            items: [],
            userToken: window.localStorage.getItem("userToken"),
            currentPage: 1,
            allPage: 1
        };
    },
    mounted() {
        this.getGameName();
    },
    methods: {
        handleCurrentChange(val) {
            this.currentPage = val;
            this.getGameById();
        },
        getGameName: function() {
            this.isLoading = true;
            console.log(this.$route.params.userToken);
            var data = [];
            this.axios
                .post("/game/all", {
                    user_token: window.localStorage.getItem("userToken")
                })
                .then(res => {
                    this.isLoading = false;
                    if (res.data.code === 200) {
                        console.log(res.data);
                        this.items = res.data.data;
                        if (res.data.data.length > 0) {
                            this.setGameName(
                                res.data.data[0].gameName,
                                res.data.data[0].gid
                            );
                        }
                    } else {
                        console.log(res.data.msg);
                    }
                });
        },
        setGameName: function(name, gid) {
            console.log(name);
            this.gameName = name;
            this.gameId = gid;
            this.getGameById();
        },
        getGameById: function() {
            this.isLoading = true;
            var data = [];
            this.axios
                .post("/user/page", {
                    user_token: this.userToken,
                    game_id: this.gameId,
                    page: this.currentPage
                })
                .then(res => {
                    this.isLoading = false;
                    if (res.data.code === 200) {
                        console.log(res.data);
                        this.tableData = res.data.data.list;
                        this.allPage = res.data.data.page * 10;
                    } else {
                        console.log(res.data.msg);
                    }
                });
        }
    }
};
</script>

<style scoped>
.table {
    width: 100%;
    margin-top: 10px;
}
.user-img {
    width: 40px;
    height: 40px;
}
.pagination {
    margin-top: 20px;
}
</style>

