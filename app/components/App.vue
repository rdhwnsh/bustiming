<template>
    <Page>
        <ActionBar title="dev.bynabil.bustimingapp" />
        <StackLayout colums="*" rows="*">

            <Label class="message bold" :text="msg" col="0" row="0" />
            <Label>Version: {{appversion}}</Label>
            <Label>Development Mode?: {{development}}</Label>
            <Label text="Click on any bus timing to know type of the bus" />

            <!-- BUS STOP CODE TEXTFIELD -->
            <Textfield v-model="stopid" />

            <Button @tap="getBusStop">{{buttontext}}</Button>

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


        </StackLayout>
    </Page>
</template>

<script>
    var Toast = require("nativescript-toast");

    export default {
        data() {
            return {
                msg: 'Enter bus stop code',
                stopid: 27301,
                data: [],
                appversion: "1.1.0",
                development: this.development_mode(),
                buttontext: "Get Timings"
            }
        },
        methods: {
            getBusStop() {
                fetch("https://arrivelah.herokuapp.com/?id=" + this.stopid)
                    .then(response => response.json())
                    .then(json => {
                        this.data = json
                        this.buttontext = "Refresh Timings"
                    })
            },
            toast_info(args) {
                let data = this.data;
                console.log(args.index);

                if(data.services[args.index].next.type == "DD"){
                    Toast.makeText("Next bus is double deck", "short").show()
                }else{
                    Toast.makeText("Next bus is single deck", "short").show()
                }
                

            },
            development_mode() {
                Toast.makeText("THIS APP IS UNDER DEVELOPMENT", "long").show()
                return true;
            },

        },

        created() {

            this.development_mode()

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
    }
</style>