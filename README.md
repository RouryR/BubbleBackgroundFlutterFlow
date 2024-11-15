# BubbleBackgroundFlutterFlow
The BubbleBG widget is a customizable Flutter background effect that generates dynamic, floating bubbles in various colors and sizes. It's ideal for adding an animated

![85ukApQzpAUK_1200_800 (1)](https://github.com/user-attachments/assets/9d4eb639-0c8c-4e78-a374-dbf054170342)

```import 'dart:math';

class BubbleBG extends StatefulWidget {
  const BubbleBG({
    Key? key,
    this.width,
    this.height,
    this.color1,
    this.color2,
    this.color3,
    this.maxBubbleSize = 30.0,
    this.bubbleSpeed = 2.0,
    this.bubbleCount = 8,
  }) : super(key: key);

  final double? width;
  final double? height;
  final Color? color1;
  final Color? color2;
  final Color? color3;
  final double maxBubbleSize;
  final double bubbleSpeed;
  final int bubbleCount;

  @override
  State<BubbleBG> createState() => _BubbleBGState();
}

class _BubbleBGState extends State<BubbleBG> with TickerProviderStateMixin {
  late AnimationController _controller;
  final List<Bubble> _bubbles = [];

  @override
  void initState() {
    super.initState();
    _controller = AnimationController(
      duration: const Duration(seconds: 10),
      vsync: this,
    )..repeat();
    _generateBubbles();
  }

  @override
  void didUpdateWidget(BubbleBG oldWidget) {
    super.didUpdateWidget(oldWidget);

    if (oldWidget.bubbleCount != widget.bubbleCount ||
        oldWidget.color1 != widget.color1 ||
        oldWidget.color2 != widget.color2 ||
        oldWidget.color3 != widget.color3 ||
        oldWidget.maxBubbleSize != widget.maxBubbleSize ||
        oldWidget.bubbleSpeed != widget.bubbleSpeed) {
      _generateBubbles();
    }
  }

  void _generateBubbles() {
    _bubbles.clear();

    final colors = [
      widget.color1 ?? Colors.red,
      widget.color2 ?? Colors.green,
      widget.color3 ?? Colors.blue,
    ];
    final random = Random();

    for (int i = 0; i < widget.bubbleCount; i++) {
      _bubbles.add(Bubble(
        position: Offset(random.nextDouble() * (widget.width ?? 400),
            random.nextDouble() * (widget.height ?? 800)),
        radius: random.nextDouble() * widget.maxBubbleSize / 2 +
            widget.maxBubbleSize / 2,
        color: colors[random.nextInt(3)],
        speed: random.nextDouble() * widget.bubbleSpeed / 2 +
            widget.bubbleSpeed / 2,
        direction: random.nextDouble() * 2 * pi,
      ));
    }
    setState(() {});
  }

  @override
  void dispose() {
    _controller.dispose();
    super.dispose();
  }

  @override
  Widget build(BuildContext context) {
    return CustomPaint(
      size: Size(
          widget.width ?? double.infinity, widget.height ?? double.infinity),
      painter: BubblePainter(_bubbles, _controller),
    );
  }
}

class Bubble {
  Bubble({
    required this.position,
    required this.radius,
    required this.color,
    required this.speed,
    required this.direction,
  });

  Offset position;
  final double radius;
  final Color color;
  final double speed;
  double direction;
}

class BubblePainter extends CustomPainter {
  BubblePainter(this.bubbles, this.animation) : super(repaint: animation);

  final List<Bubble> bubbles;
  final Animation<double> animation;

  @override
  void paint(Canvas canvas, Size size) {
    final paint = Paint()..isAntiAlias = true;

    for (final bubble in bubbles) {
      paint.color = bubble.color.withOpacity(0.5);
      canvas.drawCircle(bubble.position, bubble.radius, paint);
      _updateBubblePosition(bubble, size);
    }
  }

  void _updateBubblePosition(Bubble bubble, Size size) {
    final dx = bubble.speed * cos(bubble.direction);
    final dy = bubble.speed * sin(bubble.direction);

    bubble.position = Offset(
      bubble.position.dx + dx,
      bubble.position.dy + dy,
    );

    if (bubble.position.dx < 0 || bubble.position.dx > size.width) {
      bubble.direction = pi - bubble.direction;
    }
    if (bubble.position.dy < 0 || bubble.position.dy > size.height) {
      bubble.direction = -bubble.direction;
    }
  }

  @override
  bool shouldRepaint(covariant CustomPainter oldDelegate) => true;
} ```
