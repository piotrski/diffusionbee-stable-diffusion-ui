<template>
    <div></div>
</template>
<script>

import { send_to_py } from "./py_vue_bridge.js"

export default {
    name: 'StableDiffusion',
    props: {},
    components: {},
    mounted() {

    },
    data() {
        return {
            is_backend_loaded : false,
            is_input_avail : false,
            model_loading_msg : "",
            model_loading_title : "",
            loading_percentage : -1 , 
            attached_cbs : undefined,
        };
    },
    methods: {
        state_msg(msg){
            let msg_code = msg.substring(0, 4);
            if(msg_code == "mdld"){
                this.is_backend_loaded = true;
            }
            if(msg_code == "inrd"){
                this.is_input_avail = true;
                this.attached_cbs = undefined;
            }
            if(msg_code == "inwk"){
                this.is_input_avail = false;
            }
            if(msg_code == "nwim"){
                let impath = msg.substring(5).trim()
                if(this.attached_cbs){
                    if(this.attached_cbs.on_img)
                        this.attached_cbs.on_img(impath);
                }

            }

            if(msg_code == "mlpr"){
                let p = Number(msg.substring(5).trim());
                this.loading_percentage = p;
            }
            if(msg_code == "mlms"){
                let p = (msg.substring(5).trim());
                this.model_loading_msg = p;
            }
            if(msg_code == "mltl"){
                let p = (msg.substring(5).trim());
                this.model_loading_title = p;
            }


            if(msg_code == "errr"){
                let error = msg.substring(5).trim()
                if(this.attached_cbs){
                    if(this.attached_cbs.on_err)
                        this.attached_cbs.on_err(error);
                }

            }

            if(msg_code == "dnpr"){
                let p = Number(msg.substring(5).trim());
                if(this.attached_cbs){
                    if(this.attached_cbs.on_progress)
                        this.attached_cbs.on_progress(p);
                }

            }


        } ,

        interupt(){
            send_to_py("t2im __stop__") 
        },

        text_to_img(prompt_params, callbacks){
            if(!this.is_input_avail)
                return;
            this.attached_cbs = callbacks;
            send_to_py("t2im " + JSON.stringify(prompt_params)) 
        }

    },
}
</script>
<style>
</style>
<style scoped>
</style>