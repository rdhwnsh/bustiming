<template>
    <Page>


        <ActionBar title="bustimings" />

        <TabView tabBackgroundColor="#53ba82" androidTabsPosition="top">

            <!-- TABVIEW ITEM 1 -->
            <TabViewItem title="Home">
                <StackLayout class="addmargin">

                    <Label class="message alert" textWrap="true" :text="updateMessage" col="0" row="0" @tap="openUpdate" />
                    <Label class="message bold" :text="msg" col="0" row="0" />

                    <!-- BUS STOP CODE TEXTFIELD -->
                    <Textfield v-model="stopid" @textChange="getBusStop" @blur="__debug" />

                    <PullToRefresh @refresh="refreshList">
                        <ListView class="list-group" for="bus in data.services" style="height:1250px" @itemTap="toast_info">
                            <v-template>
                                <FlexboxLayout flexDirection="row" class="list-group-item">

                                    <!-- DISPLAY BUS NUMVER -->
                                    <Label :text="bus.no" class="list-group-item-heading bold" style="width: 60%" />

                                    <!-- DISPLAYS FIRST BUS TIMING IN MINS -->
                                    <Label class="list-group-item-heading" style="width: 60%">{{Math.floor(bus.next.duration_ms
                                        /60000)}} Mins </Label>

                                    <!-- DISPLAYS SUBSEQUENT BUS TIMING IN MINS -->
                                    <Label class="list-group-item-heading" style="width: 60%">{{Math.floor(bus.subsequent.duration_ms
                                        / 60000)}} Mins </Label>

                                </FlexboxLayout>
                            </v-template>
                        </ListView>
                    </PullToRefresh>

                </StackLayout>
            </TabViewItem>

            <!-- IDK PAGE -->
            <TabViewItem title="info">
                <StackLayout>

                    <Label>Version: {{appversion}}</Label>
                    <Label>Latest Version: {{latestversion}}</Label>
                    <Label>Development Mode?: {{development}}</Label>

                    <ListView class="list-group" for="busstop in busstophistory" style="height:1250px" @itemTap="toast_info">
                        <v-template>
                            <FlexboxLayout flexDirection="row" class="list-group-item">

                                <!-- DISPLAY BUS NUMVER -->
                                <Label :text="busstop" class="list-group-item-heading bold" style="width: 60%" />

                            </FlexboxLayout>
                        </v-template>
                    </ListView>

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
                msg: 'Enter bus stop code',
                stopid: 27301,
                data: [],
                appversion: "v1.5.0",
                development: "",
                buttontext: "Update Bus Stop",
                busstophistory: [],
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
            development_mode(bool) {
                Toast.makeText("THIS APP IS UNDER DEVELOPMENT", "long").show()
                return bool;
            },

            refreshList(args) {
                this.getBusStop()
                var pullRefresh = args.object;
                setTimeout(function () {
                    pullRefresh.refreshing = false;
                }, 200);
            },

            __debug(){
               this.busstophistory.push(this.stopid)
            },
            
            openUpdate(){
                utilsModule.openUrl("https://github.com/bynabil/nativescript-bustiming/releases")
            }

        },

        created() {
            this.development = this.development_mode(false)
            fetch("https://arrivelah.herokuapp.com/?id=" + this.stopid)
                .then(response => response.json())
                .then(json => {
                    this.data = json
                })

            fetch("https://api.github.com/repos/bynabil/nativescript-bustiming/releases")
            .then(response => response.json())
            .then(json => {
                this.latestversion = json[0].tag_name
                if(this.appversion !== this.latestversion){
                    this.updateMessage = "Update the app now! Latest version: " + this.latestversion
                    this.updateMessage.set("visibility" , "visible")

                }else{
                    this.updateMessage = "";
                    this.updateMessage.set("visibility" , "collapsed")
                }
            })
        }
    }
</script>

<style scoped>
    StackLayout {
        width: 90;
        height: 90;
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

    .alert{
        /* background-color: #ba5353; */
        color: #ba5353;
        text-align: center;
    }
</style>