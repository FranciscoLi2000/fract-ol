# fract-ol
Proyecto de CGI

从这个 **fract’ol** 项目PDF中，你可以学习到以下 **核心技能** 和 **知识体系**：

---

### **核心学习目标**
#### 1. **C语言编程与规范**
- **内存管理**
- 动态内存分配与释放（`malloc`/`free`），避免内存泄漏（Leak Check工具如`valgrind`）。
- 处理段错误（Segmentation Fault）、总线错误（Bus Error）等未定义行为。
- **代码规范**
- 严格遵守编程规范（如函数命名、缩进、文件组织），否则项目得分为零。
- 使用静态代码检查工具（如`norminette`）确保代码合规性。

#### 2. **图形编程（MiniLibX库）**
- **窗口与图像管理**
- 使用MiniLibX创建窗口、渲染图像（`mlx_init`, `mlx_new_window`, `mlx_pixel_put`）。
- 处理窗口事件（关闭、最小化、焦点切换），确保资源释放（`mlx_destroy_window`, `mlx_destroy_image`）。
- **实时交互**
- 实现鼠标滚轮无限缩放（Zoom In/Out），支持以鼠标位置为中心的缩放（Bonus）。
- 键盘事件处理（如`ESC`退出、方向键平移视图）。

#### 3. **分形算法与数学应用**
- **分形生成逻辑**
- 曼德博集（Mandelbrot Set）和朱利亚集（Julia Set）的迭代公式：
				\[ z_{n+1} = z_n^2 + c \]
- 根据迭代次数（逃逸时间算法）计算分形颜色深度。
- **复数运算**
- 复数的加减乘运算（`z = a + bi`），理解复平面与分形生成的关系。

#### 4. **用户交互与可视化优化**
- **参数化启动**
- 通过命令行参数指定分形类型（如`./fractol mandelbrot`或`./fractol julia 0.3 -0.5`）。
- 处理无效参数，显示帮助信息并优雅退出。
- **颜色映射**
- 实现颜色插值算法（如线性插值、HSV到RGB转换），增强分形视觉效果。
- 支持动态调色（Bonus），如按`C`键切换配色方案。

---

### **关键技术点解析**
#### 1. **分形渲染流程**
```c
	// 曼德博集生成伪代码
	for (int y = 0; y < HEIGHT; y++)
	{
		for (int x = 0; x < WIDTH; x++)
		{
			// 将像素坐标映射到复平面
			complex c = map_pixel_to_complex(x, y, zoom, offset);
			complex z = {0, 0};
			int iterations = 0;
			while (iterations < MAX_ITER && (z.re*z.re + z.im*z.im) < 4)
			{
				z = complex_square(z) + c; // z = z² + c
				iterations++;
			}
			// 根据迭代次数设置颜色
			set_pixel_color(img, x, y, get_color(iterations));
		}
	}
```
**关键点**：复平面映射、逃逸条件（模长平方 < 4）、迭代次数决定颜色。

#### 2. **鼠标交互与坐标变换**
```c
	// 鼠标滚轮缩放逻辑
	int	mouse_hook(int button, int x, int y, t_fractal *fractal)
	{
		if (button == MOUSE_UP)
		{
			fractal->zoom *= 1.1; // 放大
			fractal->offset_x += (x - WIDTH/2) * (1 - 1/1.1) / fractal->zoom;
			fractal->offset_y += (y - HEIGHT/2) * (1 - 1/1.1) / fractal->zoom;
		}
		else if (button == MOUSE_DOWN)
		{
			fractal->zoom /= 1.1; // 缩小
		}
		render_fractal(fractal);
		return 0;
	}
```
**关键点**：以鼠标位置为中心的缩放，需重新计算视口偏移量（`offset_x`, `offset_y`）。

#### 3. **动态颜色映射**
```c
	// HSV转RGB算法（用于彩虹色效果）
	int	hsv_to_rgb(float h, float s, float v)
	{
		int i = (int)(h * 6);
		float f = h * 6 - i;
		float p = v * (1 - s);
		float q = v * (1 - f * s);
		float t = v * (1 - (1 - f) * s);
		// 根据i计算RGB分量...
	}
```
**关键点**：通过HSV色彩空间实现平滑颜色过渡，增强分形视觉效果。

---

### **工程实践与优化**
#### 1. **模块化设计**
- **代码结构**
- 分形核心逻辑（`mandelbrot.c`, `julia.c`）。
- 图形渲染模块（`render.c`）、事件处理（`events.c`）、数学工具（`complex_math.c`）。
- **头文件管理**
- 使用`fractol.h`统一定义结构体（如`t_fractal`）和函数原型。

#### 2. **性能优化**
- **多线程渲染**
- 将屏幕分割为多个区域，使用多线程加速分形计算（仅限Bonus）。
- **预计算颜色表**
- 预生成颜色梯度数组，避免实时计算开销。

#### 3. **防御式编程**
- **错误处理**
- 检查MiniLibX初始化结果（`if (!mlx_ptr) exit(1);`）。
- 处理无效文件输入、内存分配失败，输出`Error\n`并安全退出。

---

### **扩展知识与深层价值**
1. **分形的哲学意义**
- 分形的“自相似性”反映了自然界的递归模式（如海岸线、云朵），理解数学与自然的联系。

2. **图形编程的底层原理**
- 通过MiniLibX实践，掌握像素级渲染、事件循环等图形学基础，为OpenGL/Vulkan学习铺垫。

3. **复杂系统与计算极限**
- 分形的无限细节挑战计算机的浮点精度和算力，思考优化与近似在工程中的平衡。

---

### **通关策略**
1. **分步实现**
- 优先完成曼德博集渲染，再扩展朱利亚集，最后添加交互功能。
- 使用简单配色（如黑白）验证算法正确性，再实现复杂颜色映射。

2. **测试用例**
- 测试极端参数（如超大缩放倍数、非法输入），确保程序鲁棒性。

3. **Bonus扩展**
- 实现更多分形（如燃烧船分形、牛顿迭代分形）。
- 添加音效或动画（如颜色自动渐变），提升用户体验。

---

通过此项目，你将从“命令行开发者”进阶为“图形编程实践者”，掌握复平面计算、实时交互、可视化优化等核心技能，为游戏引擎开发、科学可视化等方向打下坚实基础。
