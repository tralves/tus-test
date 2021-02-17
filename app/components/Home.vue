<template>
    <Page>
        <ActionBar>
            <Label text="Home"/>
        </ActionBar>

        <GridLayout rows="300 *">
            <Label class="info">
                <FormattedString>
                    <Span class="fas" text.decode="&#xf135; "/>
                    <Span :text="message"/>
                </FormattedString>
            </Label>
            <Button row="1" text="upload" @tap="startUpload"/>
        </GridLayout>
    </Page>
</template>

<script>
  import { File, Folder, knownFolders } from "@nativescript/core";
  import * as tus from "nativescript-tus-client";

  export default {
    computed: {
      message() {
        return "Blank {N}-Vue app";
      }
    },
    created() {
      // create a File reference
      const file = File.fromPath(knownFolders.currentApp().path + '/assets/stallman.jpg');

      console.log(file.size);

      // Create a new tus upload
      this.upload = new tus.Upload(file, {
          endpoint: "http://192.168.1.152:1080/files/",
          metadata: {
              filename: 'test_image.png',
              filetype: 'image/png'
          },
          headers: {
              Authorization: "Basic YWxhZGRpbjpvcGVuc2VzYW1l"
          },
          onError: function(error) {
              console.log("Failed because: " + error)
          },
          onProgress: function(bytesUploaded, bytesTotal) {
              var percentage = (bytesUploaded / bytesTotal * 100).toFixed(2)
              console.log(bytesUploaded, bytesTotal, percentage + "%")
          },
          onSuccess: () => {
              console.log(`Download ${this.upload.file.name} from ${this.upload.url}` )
          }
      })
    },
    methods: {
      startUpload() {
        this.upload.start();
      }
    }
  };
</script>

<style scoped lang="scss">
    @import '~@nativescript/theme/scss/variables/blue';

    // Custom styles
    .fas {
        @include colorize($color: accent);
    }

    .info {
        font-size: 20;
        horizontal-align: center;
        vertical-align: center;
    }
</style>
