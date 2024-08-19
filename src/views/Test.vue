<template>
    <div class="license-plate-generator">
        <div class="controls">
            <label>地区:</label>
            <select v-model="selectedRegion">
                <option v-for="region in regions" :key="region" :value="region">{{ region }}</option>
            </select>
            <label>平假名:</label>
            <select v-model="selectedHiragana">
                <option v-for="hiragana in hiraganas" :key="hiragana" :value="hiragana">{{ hiragana }}</option>
            </select>
            <label>大数字:</label>
            <input v-model="bigNumber" type="text" maxlength="4" />
            <label>小数字:</label>
            <input v-model="smallNumber" type="text" maxlength="2" />
        </div>
        <div class="preview">
            <canvas ref="canvas" width="237" height="122"></canvas>
        </div>
        <button @click="generateImage">生成并下载图片</button>
    </div>
</template>

<script>
export default {
    data() {
        return {
            regions: ["愛媛"], // 示例数据
            hiraganas: ["さ"], // 示例数据
            selectedRegion: "愛媛",
            selectedHiragana: "さ",
            bigNumber: "12-34",
            smallNumber: "330",
        };
    },
    watch: {
        selectedRegion: "drawPlate",
        selectedHiragana: "drawPlate",
        bigNumber: "drawPlate",
        smallNumber: "drawPlate",
    },
    mounted() {
        this.drawPlate();
    },
    methods: {
        async drawPlate() {
            const canvas = this.$refs.canvas;
            const ctx = canvas.getContext("2d");

            // 清除画布
            ctx.clearRect(0, 0, canvas.width, canvas.height);

            // 绘制地区
            const regionModule = await import(`@/assets/regions/${this.selectedRegion}.png`);
            const regionImage = new Image();
            regionImage.src = regionModule.default;
            regionImage.onload = () => {
                ctx.drawImage(regionImage, 65, 10, 65, 32); // 调整位置和大小
            };

            // 绘制平假名
            const hiraganaModule = await import(`@/assets/hiragana/${this.selectedHiragana}.png`);
            const hiraganaImage = new Image();
            hiraganaImage.src = hiraganaModule.default;
            hiraganaImage.onload = () => {
                ctx.drawImage(hiraganaImage, 10, 70, 33, 34);
            };

            // 拆分大数字为两部分
            const [part1, part2] = this.bigNumber.split('-');

            // 绘制大数字的第一部分
            for (let index = 0; index < part1.length; index++) {
                const digit = part1[index];
                const digitModule = await import(`@/assets/numbers/big/${digit}.png`);
                const digitImage = new Image();
                digitImage.src = digitModule.default;
                digitImage.onload = () => {
                    ctx.drawImage(digitImage, 50 + index * 40, 55);
                };
            }

            // 绘制固定的“-”符号图片
            const dashModule = await import('@/assets/numbers/big/-.png');
            const dashImage = new Image();
            dashImage.src = dashModule.default;
            dashImage.onload = () => {
                ctx.drawImage(dashImage, 130, 55); // 调整位置以适应数字
            };

            // 绘制大数字的第二部分
            for (let index = 0; index < part2.length; index++) {
                const digit = part2[index];
                const digitModule = await import(`@/assets/numbers/big/${digit}.png`);
                const digitImage = new Image();
                digitImage.src = digitModule.default;
                digitImage.onload = () => {
                    ctx.drawImage(digitImage, 150 + index * 40, 55);
                };
            }

            // 绘制小数字
            for (let index = 0; index < this.smallNumber.length; index++) {
                const digit = this.smallNumber[index];
                const digitModule = await import(`@/assets/numbers/small/${digit}.png`);
                const digitImage = new Image();
                digitImage.src = digitModule.default;
                digitImage.onload = () => {
                    ctx.drawImage(digitImage, 135 + index * 20, 10, 17, 31);
                };
            }
        },

        generateImage() {
            const canvas = this.$refs.canvas;
            const link = document.createElement("a");
            link.href = canvas.toDataURL("image/png");
            link.download = "license_plate.png";
            link.click();
        },
    }
};
</script>

<style scoped>
.license-plate-generator {
    text-align: center;
}

.controls {
    margin-bottom: 20px;
}

.preview {
    margin-top: 20px;
}

canvas {
    border: 1px solid #ccc;
}
</style>