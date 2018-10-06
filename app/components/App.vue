<template>
    <Page>


        <ActionBar title="dev.bynabil.bustimingapp" />
        <StackLayout colums="*" rows="*">

            <Label class="message bold" :text="msg" col="0" row="0" />
            <Label>Version: {{appversion}}</Label>
            <Label>Development Mode?: {{development}}</Label>
            <Label text="Click on any bus timing to know type of the bus" />
            <Label text="Pull to refresh (below at the bus stop numbers)" />
            <Label :text="autoRefreshMode" />

            <!-- BUS STOP CODE TEXTFIELD -->
            <Textfield v-model="stopid" />

            <!-- GET BUS STOP BUTTON -->
            <Button @tap="getBusStop">{{buttontext}}</Button>

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
    </Page>
</template>

<script>
    var Toast = require("nativescript-toast");
    const timerModule = require("tns-core-modules/timer");
    export default {
        data() {
            return {
                msg: 'Enter bus stop code',
                stopid: 27301,
                data: [],
                appversion: "1.3.0",
                development: "",
                buttontext: "Update Bus Stop",
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


                var pullRefresh = args.object;
                this.getBusStop()
                setTimeout(function () {
                    pullRefresh.refreshing = false;
                }, 1000);
            },

        },

        created() {
            this.development = this.development_mode(false)
            fetch("https://arrivelah.herokuapp.com/?id=" + this.stopid)
                .then(response => response.json())
                .then(json => {
                    this.data = json
                })
        }
    }
</script>

<style scoped>
    StackLayout {
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
</style>