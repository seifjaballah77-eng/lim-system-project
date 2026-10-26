<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <title>مشروع LimSystem</title>
    <style>
        body { margin: 0; background: #111; color: #fff; display: flex; justify-content: center; align-items: center; height: 100vh; }
    </style>
</head>
<body>
    <canvas id="canvas" width="800" height="600"></canvas>

    <script>
        // كود الـ Class الخاص بك
        class LimSystem {
            constructor(end, length, speed, creature) {
                this.creature = creature;
                this.nodes = [];
                for (var i = 0; i < length; i++) {
                    this.nodes.unshift(node);
                    node = node.parent;
                    if (!node.isSegment) {
                        this.length = i + 1;
                        break;
                    }
                }
                this.hip = this.nodes[0].parent;
            }

            update() {
                this.moveTo(Input.mouse.x, Input.mouse.y);
            }
        }

        console.log("تم تحميل الكود بنجاح!");
    </script>
</body>
</html>
