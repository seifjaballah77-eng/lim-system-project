<!DOCTYPE html>
<html lang="ar" dir="rtl">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>محاكي LimSystem التفاعلي</title>
    <style>
        * {
            margin: 0;
            padding: 0;
            box-sizing: border-box;
        }
        body {
            background-color: #0d1117;
            color: #ffffff;
            font-family: system-ui, -apple-system, sans-serif;
            overflow: hidden;
            display: flex;
            flex-direction: column;
            align-items: center;
            justify-content: center;
            height: 100vh;
        }
        #info {
            position: absolute;
            top: 20px;
            text-align: center;
            pointer-events: none;
            z-index: 10;
        }
        h1 {
            font-size: 1.5rem;
            color: #58a6ff;
            margin-bottom: 5px;
        }
        p {
            color: #8b949e;
            font-size: 0.9rem;
        }
        canvas {
            display: block;
            background: #161b22;
            box-shadow: 0 10px 30px rgba(0,0,0,0.5);
            border-radius: 12px;
        }
    </style>
</head>
<body>

    <div id="info">
        <h1>محاكي العظام التفاعلي (LimSystem)</h1>
        <p>حرّك مؤشر الفأرة داخل الصندوق لرؤية السلسلة الحركية</p>
    </div>

    <canvas id="canvas"></canvas>

    <script>
        const canvas = document.getElementById('canvas');
        const ctx = canvas.getContext('2d');

        function resizeCanvas() {
            canvas.width = Math.min(window.innerWidth - 40, 900);
            canvas.height = Math.min(window.innerHeight - 100, 600);
        }
        resizeCanvas();
        window.addEventListener('resize', resizeCanvas);

        // إعدادات مدخلات الفأرة
        const Input = {
            mouse: { x: canvas.width / 2, y: canvas.height / 2 }
        };

        canvas.addEventListener('mousemove', (e) => {
            const rect = canvas.getBoundingClientRect();
            Input.mouse.x = e.clientX - rect.left;
            Input.mouse.y = e.clientY - rect.top;
        });

        // نمذجة عقدة العظام (Node)
        class Node {
            constructor(x, y, length) {
                this.x = x;
                this.y = y;
                this.length = length;
                this.isSegment = true;
                this.parent = null;
            }
        }

        // كلاس الكائن الرئيسي المقارب لكودك الأصل
        class LimSystem {
            constructor(length, segmentLength) {
                this.nodes = [];
                let parentNode = null;

                // بناء سلسلة المفاصل والعظام
                for (let i = 0; i < length; i++) {
                    let node = new Node(canvas.width / 2, canvas.height / 2, segmentLength);
                    node.parent = parentNode;
                    this.nodes.unshift(node); // إضافة العقدة بالسلسلة
                    parentNode = node;
                }

                this.length = length;
                this.hip = this.nodes[this.nodes.length - 1]; // المفصل الأساسي
            }

            moveTo(targetX, targetY) {
                // محاكاة الحركة العكسية (Inverse Kinematics)
                let leadNode = this.nodes[0];
                leadNode.x = targetX;
                leadNode.y = targetY;

                for (let i = 1; i < this.nodes.length; i++) {
                    let currentNode = this.nodes[i];
                    let prevNode = this.nodes[i - 1];

                    let dx = currentNode.x - prevNode.x;
                    let dy = currentNode.y - prevNode.y;
                    let angle = Math.atan2(dy, dx);

                    currentNode.x = prevNode.x + Math.cos(angle) * currentNode.length;
                    currentNode.y = prevNode.y + Math.sin(angle) * currentNode.length;
                }
            }

            update() {
                this.moveTo(Input.mouse.x, Input.mouse.y);
            }

            draw(context) {
                // رسم السلسلة والمفاصل
                context.beginPath();
                context.strokeStyle = '#ff7b72';
                context.lineWidth = 6;
                context.lineCap = 'round';
                context.lineJoin = 'round';

                for (let i = 0; i < this.nodes.length; i++) {
                    let node = this.nodes[i];
                    if (i === 0) {
                        context.moveTo(node.x, node.y);
                    } else {
                        context.lineTo(node.x, node.y);
                    }
                }
                context.stroke();

                // رسم نقاط المفاصل (Nodes)
                for (let i = 0; i < this.nodes.length; i++) {
                    let node = this.nodes[i];
                    context.beginPath();
                    context.arc(node.x, node.y, i === 0 ? 8 : 4, 0, Math.PI * 2);
                    context.fillStyle = i === 0 ? '#7ee787' : '#ffa657';
                    context.fill();
                }
            }
        }

        // إنشاء كائن المحاكي (20 مفصل، طول كل جزء 18 بكسل)
        const lim = new LimSystem(20, 18);

        // حلقة التحديث والرسم المستمرة
        function animate() {
            ctx.clearRect(0, 0, canvas.width, canvas.height);
            
            lim.update();
            lim.draw(ctx);

            requestAnimationFrame(animate);
        }

        animate();
    </script>
</body>
</html>
