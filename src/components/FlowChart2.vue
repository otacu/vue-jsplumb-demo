<template>
    <div class="hello">
        <div id="relation-box">
            <div
                    class="node"
                    v-for="item in nodeList"
                    :key="item.id"
                    :style="{left: item.left, top: item.top}"
                    :id="'node-'+ item.id"
            >
                {{ item.name }}
                <div>detail...</div>
            </div>
        </div>
    </div>
</template>


<script>
    import { jsPlumb } from "jsplumb";

    export default {
        name: 'FlowChart2',
        mounted () {
            var nodes = JSON.parse('[\n' +
                '        {\n' +
                '            "id":"9b89fa90-258e-4f64-8f52-fed0516ce7d0",\n' +
                '            "label":"开始",\n' +
                '            "left":"522px",\n' +
                '            "top":"46px",\n' +
                '            "condition":null,\n' +
                '            "formData":null,\n' +
                '            "Type":1\n' +
                '        },\n' +
                '        {\n' +
                '            "id":"10ca905f-a6ee-4806-8cb9-ef14c72a6eaf",\n' +
                '            "label":"顺丰物流",\n' +
                '            "left":"554px",\n' +
                '            "top":"139px",\n' +
                '            "condition":null,\n' +
                '            "formData":{\n' +
                '                "id":"47682",\n' +
                '                "formType":"expressForm",\n' +
                '                "formData":{\n' +
                '                    "senderAddress":" ",\n' +
                '                    "expressCompany":"sf"\n' +
                '                }\n' +
                '            },\n' +
                '            "Type":3\n' +
                '        },\n' +
                '        {\n' +
                '            "id":"852fadb3-76a6-4c43-86b1-678a4271cf7f",\n' +
                '            "label":"海南订单",\n' +
                '            "left":"579px",\n' +
                '            "top":"226px",\n' +
                '            "condition":null,\n' +
                '            "formData":{\n' +
                '                "id":"47875",\n' +
                '                "formType":"hgOrderForm",\n' +
                '                "formData":{\n' +
                '                    "singlewindow":"79",\n' +
                '                    "declareName":"海南滇蓝科技有限公司"\n' +
                '                }\n' +
                '            },\n' +
                '            "Type":3\n' +
                '        },\n' +
                '        {\n' +
                '            "id":"21615545-58ac-4d55-acd7-2402419166e7",\n' +
                '            "label":"海南清单",\n' +
                '            "left":"598px",\n' +
                '            "top":"298px",\n' +
                '            "condition":null,\n' +
                '            "formData":{\n' +
                '                "id":"48148",\n' +
                '                "formType":"clearanceForm",\n' +
                '                "formData":{\n' +
                '                    "ebpName":"海南滇蓝科技有限公司",\n' +
                '                    "senderAddress":"110",\n' +
                '                    "clearanceModel":"1",\n' +
                '                    "senderCity":"110",\n' +
                '                    "webUrl":"110",\n' +
                '                    "ciqCode":"440070",\n' +
                '                    "webName":"110",\n' +
                '                    "expressCode":"SF",\n' +
                '                    "ebcName":"海南滇蓝科技有限公司",\n' +
                '                    "company":"79",\n' +
                '                    "senderCountryCode":"110",\n' +
                '                    "assureName":"海南滇蓝科技有限公司",\n' +
                '                    "senderProvinceCode":"110",\n' +
                '                    "customsCode":"6409"\n' +
                '                }\n' +
                '            },\n' +
                '            "Type":3\n' +
                '        },\n' +
                '        {\n' +
                '            "id":"dc4a4446-39aa-49f3-b72e-80f00e891573",\n' +
                '            "label":"结束",\n' +
                '            "left":"637px",\n' +
                '            "top":"398px",\n' +
                '            "condition":null,\n' +
                '            "formData":null,\n' +
                '            "Type":2\n' +
                '        }\n' +
                '    ]');
            var links = JSON.parse('[\n' +
                '        {\n' +
                '            "id":"691c2b56-30ba-4f32-93d1-ac65f36c418d",\n' +
                '            "label":"",\n' +
                '            "from":"9b89fa90-258e-4f64-8f52-fed0516ce7d0",\n' +
                '            "to":"10ca905f-a6ee-4806-8cb9-ef14c72a6eaf",\n' +
                '            "Remark":null\n' +
                '        },\n' +
                '        {\n' +
                '            "id":"d1beb595-bedf-46f6-9f4b-19a56534db89",\n' +
                '            "label":"",\n' +
                '            "from":"10ca905f-a6ee-4806-8cb9-ef14c72a6eaf",\n' +
                '            "to":"852fadb3-76a6-4c43-86b1-678a4271cf7f",\n' +
                '            "Remark":null\n' +
                '        },\n' +
                '        {\n' +
                '            "id":"6ce60f03-4267-435a-8ee1-70d74f06cfe5",\n' +
                '            "label":"",\n' +
                '            "from":"21615545-58ac-4d55-acd7-2402419166e7",\n' +
                '            "to":"dc4a4446-39aa-49f3-b72e-80f00e891573",\n' +
                '            "Remark":null\n' +
                '        },\n' +
                '        {\n' +
                '            "id":"e6bb2a42-9abb-4de9-856e-4a0a59076af8",\n' +
                '            "label":"",\n' +
                '            "from":"852fadb3-76a6-4c43-86b1-678a4271cf7f",\n' +
                '            "to":"21615545-58ac-4d55-acd7-2402419166e7",\n' +
                '            "Remark":null\n' +
                '        }\n' +
                '    ]');
            this.setNodeInfo(nodes, links);
            this.drawLines();
        },
        data () {
            return {
                jsPlumbInstance: "", //jsPlumb实例
                // jsPlumb默认配置
                jsPlumbSetting: {
                    // 动态锚点、位置自适应
                    Anchors: ['Top', 'TopCenter', 'TopRight', 'TopLeft', 'Right', 'RightMiddle', 'Bottom', 'BottomCenter', 'BottomRight', 'BottomLeft', 'Left', 'LeftMiddle'],
                    // 连线的样式 StateMachine、Flowchart，Bezier、Straight
                    Connector: ['Flowchart', { curviness: 60 }],
                    // 鼠标是否拖动删除线
                    ConnectionsDetachable: false,
                    // 删除线的时候节点不删除
                    DeleteEndpointsOnDetach: false,
                    // 连线的两端端点类型：矩形 Rectangle；圆形Dot； eight: 矩形的高 ，idth: 矩形的宽
                    Endpoints: [['Dot', { radius: 2, }], ['Dot', { radius: 2 }]],
                    // 线端点的样式
                    EndpointStyle: { fill: 'skyblue', outlineWidth: 1 },
                    // 绘制连线
                    PaintStyle: {
                        stroke: '#000000',
                        strokeWidth: 1,
                        outlineStroke: 'transparent',
                        // 设定线外边的宽，单位px
                        outlineWidth: 10
                    },
                    // 绘制连线箭头
                    Overlays: [// 箭头叠加
                        ['Arrow', {
                            width: 10, // 箭头尾部的宽度
                            length: 8, // 从箭头的尾部到头部的距离
                            location: 1, // 位置，建议使用0～1之间
                            direction: 1, // 方向，默认值为1（表示向前），可选-1（表示向后）
                            foldback: 0.623 // 折回，也就是尾翼的角度，默认0.623，当为1时，为正三角
                        }]
                    ],
                    // 绘制图的模式 svg、canvas
                    RenderMode: 'svg',
                    DragOptions: { cursor: 'pointer', zIndex: 2000 },
                    // 鼠标滑过线的样式
                    HoverPaintStyle: { stroke: 'skyblue', strokeWidth: 3, cursor: 'pointer' },
                },
                // 连线的配置
                jsPlumbConnectOptions: {
                    isSource: true,
                    isTarget: true,
                    // 动态锚点、提供了4个方向 Continuous、AutoDefault
                    anchor: "Continuous",
                    overlays: [['Arrow', { width: 8, length: 8, location: 1 }]] // overlay
                },
                commonLink: {
                    isSource: true,
                    isTarget: true,
                    anchor: ["Continuous", { shape: "Circle" }],
                    connector: ['Flowchart'],
                    endpoint: 'Dot',
                    // 不限制节点的连线数量
                    maxConnections: -1,
                },
                nodeList: [],
                lineList: []
            }
        },
        methods: {
            setNodeInfo (nodes, links) {
                // 设置节点位置信息
                this.nodeList = nodes.map(item => {
                    return {
                        id: item.id,
                        name: item.label,
                        left: item.left, // 900为初始X方向起点位置，默认为0
                        top: item.top
                    };
                });

                this.lineList = links.map(item => {
                    return {
                        source: `node-${item.from}`,
                        target: `node-${item.to}`,
                        overlays: [["Arrow", { width: 10, length: 10, location: 0.5 }]]
                    }
                })
            },
            drawLines () {
                this.$nextTick().then(() => {
                    jsPlumb.ready(() => {
                        // 创建jsPlumb实例
                        this.jsPlumbInstance = jsPlumb.getInstance();
                        // 导入准备好的jsPlumb配置
                        this.jsPlumbInstance.importDefaults(this.jsPlumbSetting);
                        // 开始节点间的连线
                        this.lineList.forEach((item) => {
                            this.jsPlumbInstance.connect(item, this.jsPlumbConnectOptions);
                        });
                        // 让每个节点都可以被拖拽
                        this.nodeList.forEach((item) => {
                            this.jsPlumbInstance.draggable("node-" + item.id);
                            // 给每个节点添加锚点
                            this.jsPlumbInstance.addEndpoint("node-" + item.id, {
                                anchor: ['Bottom', 'Top', 'Left', 'Right'],
                                Overlays: [
                                    ['Arrow', { width: 10, length: 8, location: 1, direction: 1, foldback: 0.623 }]]
                            }, this.commonLink);
                        })

                    })
                    this.jsPlumbInstance.repaintEverything(); // 重绘
                })
            }
        }

    }
</script>

<style scoped>
    #relation-box {
        position: relative;
    }

    .node {
        position: absolute;
        padding: 20px;
        border: 1px solid #ccc;
        border-radius: 20px;
        text-align: center;
        background-color: #f6f6f6;
    }
</style>

