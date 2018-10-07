<template>
    <Page>


        <ActionBar title="bustimings" />

        <TabView tabBackgroundColor="#53ba82" androidTabsPosition="bottom">

            <!-- TABVIEW ITEM 1 -->
            <TabViewItem title="Home">
                <PullToRefresh @refresh="refreshList">
                    <StackLayout class="addmargin">

                        <Label class="message alert" textWrap="true" :text="updateMessage" col="0" row="0" @tap="openUpdate" />
                        <Label class="message bold" col="0" row="0">Bus Stop Code</Label>

                        <!-- BUS STOP CODE TEXTFIELD -->
                        <Textfield v-model="stopid" @textChange="getBusStop" />

                        <ListView class="list-group" for="bus in data.services" style="height:1250px" @itemTap="toast_info">
                            <v-template>
                                <FlexboxLayout flexDirection="row" class="list-group-item">

                                    <!-- DISPLAY BUS NUMVER -->
                                    <Label :text="bus.no" class="list-group-item-heading bold" style="width: 60%" />

                                    <StackLayout v-if="Math.floor(bus.next.duration_ms/60000) <= 1">
                                        <!-- DISPLAYS FIRST BUS TIMING IN MINS -->
                                        <Label class="list-group-item-heading" style="width: 60%">Arr</Label>
                                    </StackLayout>

                                    <StackLayout v-else>
                                        <!-- DISPLAYS FIRST BUS TIMING IN MINS -->
                                        <Label class="list-group-item-heading" style="width: 60%">{{Math.floor(bus.next.duration_ms
                                            /60000)}} Mins </Label>
                                    </StackLayout>

                                    <!-- Subsequent bus -->
                                    <StackLayout v-if="Math.floor(bus.subsequent.duration_ms/ 60000) <= 1">
                                        <!-- DISPLAYS FIRST BUS TIMING IN MINS -->
                                        <Label class="list-group-item-heading" style="width: 60%">Arr</Label>
                                    </StackLayout>

                                    <StackLayout v-else>
                                        <!-- DISPLAYS FIRST BUS TIMING IN MINS -->
                                        <Label class="list-group-item-heading" style="width: 60%">{{Math.floor(bus.subsequent.duration_ms
                                        / 60000)}} Mins </Label>
                                    </StackLayout>
                                </FlexboxLayout>
                            </v-template>
                        </ListView>

                    </StackLayout>
                </PullToRefresh>
            </TabViewItem>

            <!-- IDK PAGE -->
            <TabViewItem title="info">
                <StackLayout>
                    
                    <Label>Version: {{appversion}}</Label>
                    <Label>Latest Version: {{latestversion}}</Label>

                </StackLayout>
            </TabViewItem>

        </TabView>


    </Page>
</template>

<script>
    var Toast = require("nativescript-toast");
    var utilsModule = require("tns-core-modules/utils/utils");

    export default {
        data() {
            return {
                stopid: 27301,
                data: [],
                appversion: "v1.6.0",
                buttontext: "Update Bus Stop",
                latestversion: "",
            }
        },
        methods: {

            getBusStop() {
                fetch("https://arrivelah.herokuapp.com/?id=" + this.stopid)
                    .then(response => response.json())
                    .then(json => {
                        this.data = json
                    })
            },
            toast_info(args) {
                let data = this.data;
                console.log(args.index);

                if (data.services[args.index].next.type == "DD") {
                    Toast.makeText("Next bus is double deck", "short").show()
                } else {
                    Toast.makeText("Next bus is single deck", "short").show()
                }


            },
            refreshList(args) {
                this.getBusStop()
                var pullRefresh = args.object;
                setTimeout(function () {
                    pullRefresh.refreshing = false;
                }, 200);

                this.getLatestVersion()
            },

            openUpdate() {
                utilsModule.openUrl("https://github.com/ridhwanshah/bustiming/blob/master/platforms/android/app/build/outputs/apk/debug/app-debug.apk?raw=true")
            },

            getLatestVersion() {
                fetch("https://api.github.com/repos/ridhwanshah/bustiming/releases")
                    .then(response => response.json())
                    .then(json => {
                        this.latestversion = json[0].tag_name
                        if (this.appversion !== this.latestversion) {
                            this.updateMessage = "Update the app now! Latest version: " + this.latestversion

                        } else {
                            this.updateMessage = "";
                        }
                    })
            },

        },

        mounted() {
            this.getBusStop()
            this.getLatestVersion();

        }
    }
</script>

<style scoped>
    TabViewItem {
        width: 90%;
        height: 90%;
    }

    ActionBar {
        background-color: #53ba82;
        color: #ffffff;
    }

    Label {
        vertical-align: center;
    }

    .message {
        /* vertical-align: center; */
        /* text-align: center; */
        font-size: 20;
        color: #333333;
    }

    .bold {
        font-weight: bold;
        font-size: 30;
    }

    Button {
        background-color: #53ba82;
        color: #ffffff;
        padding: 10;
    }

    .alert {
        /* background-color: #ba5353; */
        color: #ba5353;
        text-align: center;
    }
</style>