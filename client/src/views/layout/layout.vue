<template>
    <div class="page_root" v-resize="onResize">
        <!--菜单开始-->
        <v-navigation-drawer
            v-model="menuDrawer"
            :mini-variant="miniVariant"
            disable-resize-watcher
            mini-variant-width="74"
            class="page_drawer"
            absolute
            dark
            style="z-index: 4"
        >
            <v-img
                v-slot:img
                src="../../assets/img/sidebar-1.jpg"
                gradient="rgba(0, 0, 0, 0.8), rgba(0, 0, 0, 0.8)"
            />

            <v-list nav class="py-0">
                <!-- <v-list-item  @click="a=>a" style="padding-top:8px; margin-bottom: 0" target="_blank" href="https://vuetifyjs.com">
                    <v-list-item-avatar size="30">
                        <img src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-light.png" />
                    </v-list-item-avatar>
                    <v-list-item-content>
                        <v-list-item-title class="headline">Material-UI</v-list-item-title>
                    </v-list-item-content>
                </v-list-item>
                <v-divider></v-divider> -->

                <v-list-item @click="onAxios" style="margin-top: 8px">
                    <!-- <v-list-item-avatar>
                        <img :src="tx" />
                    </v-list-item-avatar>-->
                    <v-list-item-avatar size="40">
                        <img
                            src="https://cdn.vuetifyjs.com/images/logos/vuetify-logo-light.png"
                        />
                        <!-- <img src="https://material-ui.com/static/logo_raw.svg" /> -->
                    </v-list-item-avatar>
                    <v-list-item-content>
                        <v-list-item-title class="title"
                            >货物仓储管理系统</v-list-item-title
                        >
                        <v-list-item-subtitle></v-list-item-subtitle>
                    </v-list-item-content>
                </v-list-item>
                <!-- <v-divider></v-divider> -->
            </v-list>
            <v-list nav class="py-0" style="margin-top: 20px">
                <template v-for="(item, index) in menus">
                    <template
                        v-if="
                            item.visible &&
                            item.children &&
                            item.children.length > 1
                        "
                    >
                        <v-list-group
                            :key="item.path"
                            :prepend-icon="item.meta.icon"
                            :group="item.name"
                            active-class="v_list_group_active"
                            :value="checkMenuGroupValue(item.path)"
                        >
                            <template v-slot:activator>
                                <v-list-item-content>
                                    <v-list-item-title>{{
                                        $t("header." + item.meta.title)
                                    }}</v-list-item-title>
                                </v-list-item-content>
                            </template>
                            <v-list-item
                                v-for="(child, key) in item.children"
                                :key="key"
                                :to="{ name: child.name }"
                                active-class="primary"
                            >
                                <v-list-item-avatar>
                                    <v-icon v-text="child.meta.icon"></v-icon>
                                </v-list-item-avatar>
                                <v-list-item-content>
                                    <v-list-item-title>{{
                                        child.meta.title
                                    }}</v-list-item-title>
                                </v-list-item-content>
                            </v-list-item>
                        </v-list-group>
                    </template>
                    <template v-else>
                        <v-list-item
                            v-if="item.visible"
                            :key="index"
                            :to="{ path: item.path }"
                            active-class="primary"
                        >
                            <v-list-item-icon>
                                <v-icon>{{ item.meta.icon }}</v-icon>
                            </v-list-item-icon>
                            <v-list-item-content>
                                <v-list-item-title>{{
                                    $t("" + item.meta.title)
                                }}</v-list-item-title>
                            </v-list-item-content>
                        </v-list-item>
                    </template>
                </template>
            </v-list>
        </v-navigation-drawer>
        <!--菜单结束-->
        <!--主体开始-->
        <v-main
            class="page_right_content"
            :class="{ miniVariant: miniVariant, darkMode: darkMode }"
        >
            <v-toolbar absolute class="header" flat>
                <!--处理显示导航菜单-->
                <v-btn
                    fab
                    small
                    style="margin-right: 18px"
                    @click="handleMenuDrawer"
                    v-if="!menuDrawer"
                >
                    <v-icon v-if="menuDrawer">mdi-drag</v-icon>
                    <v-icon v-else>mdi-drag-horizontal</v-icon>
                </v-btn>
                <!--处理导航菜单mini样式-->
                <v-btn
                    fab
                    small
                    style="margin-right: 18px"
                    @click="handleMiniMenu"
                    v-else
                >
                    <v-icon v-if="!miniVariant">mdi-drag</v-icon>
                    <v-icon v-else>mdi-drag-horizontal</v-icon>
                </v-btn>
                <!-- <v-btn small text disabled></v-btn> -->
                <v-toolbar-title style="text-transform: capitalize">{{
                    pageTitle
                }}</v-toolbar-title>
                <v-spacer></v-spacer>
                <v-menu
                    v-model="noticeVisible"
                    :close-on-content-click="false"
                    max-width="280px"
                    offset-y
                    open-on-hover
                >
                    <template v-slot:activator="{ on }">
                        <v-badge
                            content="9"
                            value="9"
                            :offset-x="30"
                            :offset-y="20"
                            overlap
                            bordered
                        >
                            <v-btn text v-on="on">
                                <v-icon>mdi-bell</v-icon>
                            </v-btn>
                        </v-badge>
                    </template>

                    <v-card>
                        <v-list three-line>
                            <template v-for="(item, index) in items">
                                <v-subheader
                                    v-if="item.header"
                                    :key="item.header"
                                    v-text="item.header"
                                ></v-subheader>
                                <v-divider
                                    v-else-if="item.divider"
                                    :key="index"
                                    :inset="item.inset"
                                ></v-divider>
                                <v-list-item
                                    v-else
                                    :key="item.title"
                                    @click="() => {}"
                                >
                                    <v-list-item-avatar>
                                        <v-img :src="item.avatar"></v-img>
                                    </v-list-item-avatar>
                                    <v-list-item-content>
                                        <v-list-item-title
                                            v-html="item.title"
                                        ></v-list-item-title>
                                        <v-list-item-subtitle
                                            v-html="item.subtitle"
                                        ></v-list-item-subtitle>
                                    </v-list-item-content>
                                </v-list-item>
                            </template>
                        </v-list>
                        <v-divider></v-divider>
                        <v-card-actions>
                            <v-spacer></v-spacer>
                            <v-btn
                                color="primary"
                                text
                                @click="noticeVisible = false"
                                >Clear</v-btn
                            >
                        </v-card-actions>
                    </v-card>
                </v-menu>
                <v-btn
                    text
                    target="_blank"
                    href="https://github.com/Groundhog-Chen/vue-material-admin"
                    class="min_hide"
                >
                    <v-icon
                        title="https://github.com/Groundhog-Chen/vue-material-admin"
                        >mdi-github-circle</v-icon
                    >
                </v-btn>
                <v-btn text @click="fullScreen" class="min_hide">
                    <v-icon>mdi-arrow-expand-all</v-icon>
                </v-btn>
                <v-menu
                    bottom
                    :close-on-content-click="false"
                    :offset-y="true"
                    open-on-hover
                >
                    <template v-slot:activator="{ on }">
                        <v-btn text v-on="on">
                            <v-icon>mdi-settings</v-icon>
                        </v-btn>
                    </template>
                    <v-card style="min-width: 220px; padding-bottom: 20px">
                        <v-subheader>Color Option</v-subheader>
                        <v-list subheader>
                            <template v-for="(item, key) in colors">
                                <v-list-item
                                    :key="key + 2"
                                    :class="{
                                        'v-list-item--active': item.active,
                                    }"
                                    @click="handleChangeColor(item.color, key)"
                                >
                                    <v-list-item-avatar
                                        :color="item.color"
                                        :size="25"
                                    ></v-list-item-avatar>
                                    <v-list-item-content>
                                        <v-list-item-subtitle>{{
                                            item.name
                                        }}</v-list-item-subtitle>
                                    </v-list-item-content>
                                </v-list-item>
                            </template>
                        </v-list>
                        <v-subheader>Language</v-subheader>
                        <v-radio-group
                            v-model="Language"
                            @change="handleCutover"
                            style="margin: 0 20px"
                        >
                            <v-radio
                                label="中文"
                                value="zh_CN"
                                color="primary"
                            ></v-radio>
                            <v-radio
                                label="English"
                                value="en_US"
                                color="primary"
                            ></v-radio>
                        </v-radio-group>
                        <v-subheader>Dark Mode</v-subheader>
                        <v-switch
                            v-model="darkMode"
                            @change="onDarkModeChange"
                            style="margin-left: 20px"
                        ></v-switch>

                        <div class="login_out" @click="login_out">退出登录</div>
                    </v-card>
                </v-menu>
                <!-- <v-btn text @click="settingsVisible = true">
					<v-icon>mdi-settings</v-icon>
                </v-btn>-->
            </v-toolbar>
            <div class="zwf"></div>
            <transition name="fade-transform" mode="out-in">
                <keep-alive :key="curTime">
                    <router-view v-if="$route.meta.keepAlive" />
                </keep-alive>
            </transition>
            <transition name="fade-transform" mode="out-in">
                <router-view
                    v-if="!$route.meta.keepAlive"
                    :key="curTime"
                ></router-view>
            </transition>
        </v-main>
        <!--主体结束-->
    </div>
</template>
<script>
// import axios from 'axios';

export default {
    data() {
        return {
            tx: require('../../assets/wx.png'),
            menuDrawer: true,
            expandOnHover: false,
            noticeVisible: false,
            bg: {
                'src':
                    'https://demos.creative-tim.com/material-dashboard-pro/assets/img/sidebar-1.jpg',
                'linear-gradient':
                    'to top right, rgba(0, 0, 0, 0.8), rgba(0, 0, 0, 0.8)'
            },
            miniVariant: false,
            Language: 'en_US',
            settingsVisible: false,
            isFullScreen: false,
            name: '',
            desc: '',
            token: '',
            news: [],
            admins: [
                ['Management', 'people_outline'],
                ['Settings', 'settings'],
            ],
            cruds: [
                ['Create', 'add'],
                ['Read', 'insert_drive_file'],
                ['Update', 'update'],
                ['Delete', 'delete'],
            ],
            items: [
                { header: 'Today' },
                {
                    avatar: 'https://cdn.vuetifyjs.com/images/lists/4.jpg',
                    title: 'Birthday gift',
                    subtitle: '<span class=\'text--primary\'>Trevor Hansen</span> &mdash; Have any ideas about what we should get Heidi for her birthday?',
                },
                { divider: true, inset: true },
                {
                    avatar: 'https://cdn.vuetifyjs.com/images/lists/5.jpg',
                    title: 'Recipe to try',
                    subtitle: '<span class=\'text--primary\'>Britta Holt</span> &mdash; We should eat this: Grate, Squash, Corn, and tomatillo Tacos.',
                },
            ],
        };
    },
    computed: {
        loadPaths() {
            return this.$route.path.split('/');
        },
        curTime() {
            return this.$store.state.curTime;
        },
        pageTitle() {
            return this.$route.meta.title;
        },
        locale(key) {
            return this.$t('header.' + key);
        },
        darkMode: {
            get: function () {
                return this.$store.state.darkMode;
            },
            set: function (newValue) {
                this.$store.state.darkMode = newValue;
            }
        },
        menus() {
            const { options } = this.$router;
            // return options.routes[0].children.map((item) => {
            //     item['active'] = false;
            //     return item;
            // });
            return options.routes;
        },
        colors() {
            return this.$store.state.colors;
        }
    },
    created() {
        // console.log(this.$route);
        // axios.request({
        //     url: '/news',
        //     method: 'get',
        //     baseURL: 'http://127.0.0.1:7001'
        // }).then((res) => {
        //     console.log(res);
        //     this.news = res.data.result;
        // });
    },
    methods: {
        onResize(e) {
            if (!e) return;
            const { innerWidth } = e.srcElement;
            if (innerWidth > 1250) {
                // console.log('BOOM');
                this.menuDrawer = true;
            }
        },
        onDarkModeChange(val) {
            this.$vuetify.theme.dark = val;
            this.$store.commit('handleDarkMode', val);
        },
        checkMenuGroupValue(path) {
            const arr = path.split('/');
            if (!arr[1]) {
                return false;
            }
            return this.loadPaths.includes(arr[1]);
        },
        handleMenuDrawer() {
            this.menuDrawer = !this.menuDrawer;
        },
        handleMiniMenu() {
            this.miniVariant = !this.miniVariant;
        },
        onAxios() {
            // const data = {
            //     title: this.name,
            //     desc: this.desc
            // };
            // axios.get(url,AxiosRequestConfig).then((res) => {
            //     console.log(res);
            // });
            // const token = this.token;
            // axios.request({
            //     url: '/news/create',
            //     method: 'post',
            //     baseURL: 'http://127.0.0.1:7001',
            //     data: data,
            //     headers: {
            //         'x-csrf-token': token
            //     }
            // });
            window.open('https://github.com/Groundhog-Chen/vue-material-admin');
        },
        tmyx() {
            this.$store.commit('handleChangeMlmlh');
        },
        tmlx() {
            this.$store.commit('handleChangeYmlmlh');
        },
        handleChangeColor(color, key) {
            this.$vuetify.theme.themes.light.primary = color;
            this.$store.commit('handleSetColor', key);
            this.$vuetify.theme.dark = false;
            this.$store.commit('handleDarkMode', false);
        },
        handleCutover(val) {
            this.$i18n.locale = val;
        },
        handleSignOut() {
            this.$store.commit('handleSignOut');
            setTimeout(() => {
                this.$router.push('/login');
            }, 300);
        },
        fullScreen() {
            const el = document.documentElement;
            const rfs =
                el.requestFullScreen ||
                el.webkitRequestFullScreen ||
                el.mozRequestFullScreen ||
                el.msRequestFullscreen;
            if (typeof rfs != 'undefined' && rfs) {
                rfs.call(el);
            }
            this.isFullScreen = true;
            return;
        },

        // 退出登录按钮的点击
        login_out() {
            this.$confirm('是否退出当前账号?', '提示', {
                confirmButtonText: '确定',
                cancelButtonText: '取消',
                type: 'warning'
            }).then(() => {
                // 清空token
                localStorage.removeItem('token')

                this.$message({
                    type: 'success',
                    message: '退出成功, 即将跳转到登录页面!'
                });

                setTimeout(() => {
                    // 更新Vuex状态
                    this.$store.commit('handleSignOut')
                }, 2000);


                setTimeout(() => {
                    // 跳转到登录页面
                    this.$router.push('/login')
                }, 2500);
            });




        }
    },
    // beforeRouteLeave(to, from, next) {
    //     // to.meta.keepAlive = false;
    //     // console.log('1')
    //     next();
    // }
};
</script>

<style scoped>
.login_out {
    height: 50px;
    text-align: center;
    line-height: 50px;
    transition: all 150ms linear;
}

.login_out:hover {
    box-shadow: 0 4px 20px 0px rgb(0 0 0 / 14%),
        0 7px 10px -5px rgb(156 39 176 / 40%);
    background-color: #9c27b0;
    color: #ffffff;
    cursor: pointer;
}
</style>
