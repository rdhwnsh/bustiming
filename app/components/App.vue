<template>
    <Page>
        <ActionBar title="dev.bynabil.bustimingapp" />
        <StackLayout colums="*" rows="*">

            <Label class="message bold" :text="msg" col="0" row="0" />

            <Textfield v-model="stopid" />

            <Button @tap="getBusStop">Get Timings</Button>

            <ListView class="list-group" for="bus in data.services" style="height:1250px">
                <v-template>
                    <FlexboxLayout flexDirection="row" class="list-group-item">

                        <!-- DISPLAY BUS NUMVER -->
                        <Label :text="bus.no" class="list-group-item-heading bold" style="width: 60%" />

                        <!-- DISPLAYS FIRST BUS TIMING IN MINS -->
                        <Label class="list-group-item-heading" style="width: 60%">{{Math.floor(bus.next.duration_ms /60000)}} Mins </Label>
                        <Label class="list-group-item-heading" style="width: 60%">{{bus.next.type}}</Label>

                        <!-- DISPLAYS SUBSEQUENT BUS TIMING IN MINS -->
                        <Label class="list-group-item-heading" style="width: 60%">{{Math.floor(bus.subsequent.duration_ms / 60000)}} Mins </Label>
                        <Label class="list-group-item-heading" style="width: 60%">{{bus.subsequent.type}} </Label>

                    </FlexboxLayout>
                </v-template>
            </ListView>


        </StackLayout>
    </Page>
</template>

<script>
    export default {
        data() {
            return {
                msg: 'Enter bus stop code',
                stopid: 27301,
                data: []
            }
        },
        methods: {
            getBusStop() {
                fetch("https://arrivelah.herokuapp.com/?id=" + this.stopid)
                    .then(response => response.json())
                    .then(json => {
                        this.data = json
                    })
            }
        },

        created() {
            fetch("https://arrivelah.herokuapp.com/?id=" + this.stopid)
                .then(response => response.json())
                .then(json => {
                    this.data = json
                })
        }
    }
</script>

<style scoped>
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