<template>
    <div :class="$style.box">
        <el-form-item
            :label="selectProps.title"
            :prop="curNodePath"
            :class="$style.elFormItem"
            :rules="[
                {
                    validator(rule, value, callback) {
                        const validProperties = ['imgUrl', 'imgLink'];

                        // 针对叶子节点做校验
                        let errors = [];
                        const isValidate = validProperties.every(item => {
                            errors = schemaValidate.validateFormDataAndTransformMsg({
                                formData: value[item],
                                schema: $props.schema.properties[item],
                                customFormats: $props.customFormats,
                                errorSchema: $props.errorSchema[item],
                                required: $props.schema.required.includes(item),
                                propPath: $props.curNodePath
                            });
                            return errors.length <= 0;
                        });

                        if (isValidate) return callback();

                        return callback(errors[0].message);
                    },
                }
            ]"
            :required="elItemRequired"
        >
            <div
                v-if="selectProps.description"
                :class="$style.description"
                v-html="selectProps.description"
            ></div>
            <div :class="$style.formItem">
                <div
                    :class="$style.uploadBox"
                    @click="selectImg"
                >
                    <img
                        v-if="imgUrl"
                        :src="imgUrl"
                        alt=""
                        style="max-width: 100%;max-height: 100%;"
                    >
                    <i
                        v-else
                        class="el-icon-plus"
                    ></i>
                </div>
                <el-input
                    v-model="imgLink"
                    :class="$style.input"
                    :placeholder="placeholder"
                    size="medium"
                ></el-input>
            </div>
        </el-form-item>
    </div>
</template>

<script>
    // 覆盖默认field 做个性商品选择和链接输入
import {
    fieldProps,
    vueUtils,
    formUtils,
    schemaValidate
} from '@lljj/vue-json-schema-form';

export default {
    name: 'LinkImgField',
    props: {
        ...fieldProps,
        fieldProps: {
            type: null,
            default: null
        }
    },
    data() {
        return {
            schemaValidate,
            vueUtils
        };
    },
    computed: {
        elItemRequired() {
            // 配置了 required 的属性提示小红点
            return this.schema.required.length > 0;
        },
        placeholder() {
            const imgLinkOptions = formUtils.getUiOptions({
                schema: this.schema.properties.imgLink,
                uiSchema: this.uiSchema.imgLink || {}
            });
            return imgLinkOptions.placeholder || '请输入合法的链接';
        },
        selectProps() {
            return formUtils.getUiOptions({
                schema: this.schema,
                uiSchema: this.uiSchema
            });
        },
        curValue() {
            return vueUtils.getPathVal(this.rootFormData, this.curNodePath);
        },
        imgUrl: {
            get() {
                return this.curValue.imgUrl;
            },
            set(value) {
                vueUtils.setPathVal(this.rootFormData, vueUtils.computedCurPath(this.curNodePath, 'imgUrl'), value);
            }
        },
        imgLink: {
            get() {
                return this.curValue.imgLink;
            },
            set(value) {
                vueUtils.setPathVal(this.rootFormData, vueUtils.computedCurPath(this.curNodePath, 'imgLink'), value);
            }
        }
    },
    methods: {
        selectImg() {
            const imgs = [
                'https://m.360buyimg.com/babel/jfs/t1/136164/18/4633/216335/5f1176b9E0d2e6f59/e41c72e01932fd73.jpg.webp',
                'https://gw.alicdn.com/tfs/TB1DKP9zCtYBeNjSspkXXbU8VXa-1920-450.jpg_Q90.jpg',
                'https://aecpm.alicdn.com/simba/img/TB1W4nPJFXXXXbSXpXXSutbFXXX.jpg',
                'https://aecpm.alicdn.com/simba/img/TB1_JXrLVXXXXbZXVXXSutbFXXX.jpg',
                'https://img.alicdn.com/tfs/TB1FrlZPAzoK1RjSZFlXXai4VXa-1000-320.jpg',
                'https://img.alicdn.com/tfs/TB1n5sCMYvpK1RjSZPiXXbmwXXa-900-320.jpg',
                'https://img.alicdn.com/tps/i4/TB1ecCsOCzqK1RjSZPxSuw4tVXa.jpg',
                'https://img.alicdn.com/tps/i4/TB1tVhuNhnaK1RjSZFBSuwW7VXa.jpg',
                'https://img.alicdn.com/tfs/TB1IyonQVXXXXXCXXXXXXXXXXXX-750-200.jpg',
                'https://gw.alicdn.com/tfs/TB1hJ2KX6ihSKJjy0FlXXadEXXa-254-318.png',
                'https://gw.alicdn.com/tfs/TB1UE5RaCWD3KVjSZSgXXcCxVXa-720-400.jpg',
                'https://gw.alicdn.com/tfs/TB11iC2uAzoK1RjSZFlXXai4VXa-254-318.jpg',
                'https://gw.alicdn.com/tfs/TB1xo26qeH2gK0jSZFEXXcqMpXa-330-316.jpg',
                'https://img.alicdn.com/bao/uploaded/i3/2781891994/O1CN01usHqqQ1QbILCMqrJm_!!2781891994.jpg',
                'https://img.alicdn.com/bao/uploaded/i1/TB1M31ANFXXXXaOXpXXwu0bFXXX.png',
                'https://img.alicdn.com/imgextra/i2/143584903/O1CN01qdnUD81m5cPPJlXog_!!143584903.jpg',
                'https://img.alicdn.com/tps/i4/TB1Q2Mnd2zO3e4jSZFxwu1P_FXa.png_500x1000q75.jpg_.webp',
                'https://img.alicdn.com/tps/i4/TB1t2dzOvb2gK0jSZK9wu1EgFXa.png_500x1000q75.jpg_.webp',
                'https://img.alicdn.com/tps/i4/TB1ZJtFOAL0gK0jSZFAwu3A9pXa.png_500x1000q75.jpg_.webp',
                'https://img.alicdn.com/tps/i4/TB1y4tuOxz1gK0jSZSgSuuvwpXa.jpg_500x1000q75s0.jpg_.webp',
                'https://img.alicdn.com/tps/i4/TB1y4tuOxz1gK0jSZSgSuuvwpXa.jpg_500x1000q75s0.jpg_.webp',
                'https://img.alicdn.com/tps/i4/TB1fbhiawoQMeJjy0FnSuv8gFXa.jpg_490x490q100.jpg_.webp',
                'https://gw.alicdn.com/tfs/TB1UzOqoWL7gK0jSZFBXXXZZpXa-468-602.jpg',
                'https://img.alicdn.com/tfs/TB1XjMYnfb2gK0jSZK9XXaEgFXa-468-1236.jpg',
                'https://img.alicdn.com/tps/i4/TB1MesKcWmWQ1JjSZPhwu0CJFXa.png',
                'https://gw.alicdn.com/tfs/TB1xVR9oFP7gK0jSZFjXXc5aXXa-468-602.jpg',
                'https://img.alicdn.com/tps/i4/TB1R8tlXxvbeK8jSZPfSuuriXXa.jpg_490x490q100.jpg_.webp'
            ];
            this.$message.success('选择图片成功，随机一个图片');
            this.imgUrl = imgs[Math.floor(Math.random() * imgs.length)];
        }
    }
};
</script>

<style module>
    @import 'yongjiu_vue_editor_common/css/variable.css';
    .box {
        :global {
            .el-form-item.is-error {
                :local {
                    .uploadBox {
                        color: #F56C6C;
                    }
                }
            }
        }
    }
    :global {
        .arrayOrderList_item {
            :local {
                .elFormItem {
                    margin-bottom: 0;
                }
            }
        }
    }
    .formItem {
        align-items: center;
        display: flex;
    }
    .input {
        flex: 1;
        margin-left: 15px;
    }
    .description {
        font-size: 12px;
        line-height: 20px;
        margin-bottom: 10px;
        color: var(--color-text-light)
    }
    .uploadBox {
        cursor: pointer;
        width: 80px;
        height: 50px;
        display: flex;
        align-items: center;
        justify-content: center;
        background-color: #e2e2e2;
    }
</style>
