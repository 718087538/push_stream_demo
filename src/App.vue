<template>
  <div id="app">
    <h1>live_demo</h1>
    <div id="local_stream"></div>
    <hr>
    <!-- <h2>订阅的流</h2>
    <div id="play_stream"></div> -->
  </div>
</template>

<script>
// import AgoraRTC from "agora-rtc-sdk";
import AgoraRTC from "agora-rtc-sdk";
export default {
  name: "App",
  data() {
    return {
      a: "ccc",
      rtc: {
        client: null,
        joined: false,
        published: false,
        localStream: null,
        remoteStreams: [],
        params: {}
      },
      option: {
        appID: "1e93df4848bf40119a2276cf5b9a3a5c",
        channel: "1",
        uid: null,
        token:
          "0061e93df4848bf40119a2276cf5b9a3a5cIADaupOXOCtyNTV00H2JP0JSoiViGlmo4xlyPvw/D8fkPbfv3IMAAAAAEAB2N7Fhcm3wXgEAAQBxbfBe"
      }
    };
  },
  components: {},
  beforeMount() {
    console.log("beforeMount");
    // 1，创建客户端
    this.rtc.client = AgoraRTC.createClient({ mode: "live", codec: "vp8" });
    // 2，初始化客户端
    let that = this;
    this.rtc.client.init(
      this.option.appID,
      function() {
        console.log("客户端init成功");

        that.rtc.client.setClientRole("host");

        // console.log("UID", that.option.uid);
        // 3，加入频道
        // Join a channel
        that.rtc.client.join(
          that.option.token ? that.option.token : null,
          that.option.channel,
          that.option.uid ? +that.option.uid : null,

          function(uid) {
            //已经onSuccess
            // alert();
            that.$message({
              message:
                "加入频道: " + that.option.channel + " success, uid: " + uid,
              type: "success"
            });
            that.rtc.params.uid = uid;

            // Create a local stream
            that.rtc.localStream = AgoraRTC.createStream({
              streamID: that.rtc.params.uid,
              audio: true,
              video: true,
              screen: false
            });

            // Initialize the local stream
            that.rtc.localStream.init(
              function() {
                console.log("init local stream success");
                // play stream with html element id "local_stream"
                that.rtc.localStream.play("local_stream");

                // Publish the local stream
                that.rtc.client.publish(that.rtc.localStream, function(err) {
                  console.log("推流失败");
                  console.error(err);
                });
              },
              function(err) {
                console.error("init local stream failed ", err);
              }
            );
            console.log("本地流", that.rtc.localStream);
          },
          function(err) {
            console.error("client join failed", err);
          }
        );
      },
      err => {
        console.error(err);
      }
    );
  }
};
</script>

<style>
#local_stream {
  width: 300px;
  height: 300px;
}
</style>
