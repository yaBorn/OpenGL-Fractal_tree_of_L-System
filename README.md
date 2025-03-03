# Fractal_tree_of_L-System
【C++/OpenGL】基于L系统随机文法元的分形树
===
转载与调用请保留并注释原作者yaBorn的原文地址：https://github.com/yaBorn/-OpenGL-Fractal_tree_of_L-System

TODO:  
  1.报告编写 OK  
  2.加入图片ing  
  3.整理cpp
---
班级：数媒1803班  
学号：1191180331  

此处应有图片*1(可能未显示，图片在<im_rd>中)  
深层迭代情况下的树，可以看出由三个文法元，随机迭代，层数够深时和自然状态的树很接近了。(不过深层迭代的性能8太行)  
 ![](https://github.com/yaBorn/-OpenGL-Fractal_tree_of_L-System/blob/main/im_md/1.png "深层迭代")

目录
===
* 运行说明
* 编写环境
* 调试说明
* 运行操作
* 效果
* 代码说明
  * 思路
  * 代码结构
  * 文法生成代码说明
  * 着色器渲染代码说明
* 许可

运行说明
===
* 直接下载解压<可执行程序_L分形树.rar>，内有dll，bmp*2，exe，点击exe运行即可。  
* 下载右侧发布Releases<_L.rar>也可以。 
* 若提示找不到 msvcp140d.dll / ucrtbased.dll / vcruntime140d.dll，将对应dll下载置于根目录即可。

此处应有动图*2(可能未显示，图片在<im_rd>中)    
 ![](https://github.com/yaBorn/-OpenGL-Fractal_tree_of_L-System/blob/main/im_md/%E5%88%87%E6%8D%A2%E6%96%87%E6%B3%95.gif "切换迭代文法")
 ![](https://github.com/yaBorn/-OpenGL-Fractal_tree_of_L-System/blob/main/im_md/%E8%A7%86%E8%A7%92.gif "视角")

编写环境
===
* VStudio 2017 (安装NuGet包管理器)  
* nupengl.core / nupengl.core.redist [dll下载](https://www.opengl.org/resources/libraries/glut/glutdlls37beta.zip)  
* [GLUT](https://www.opengl.org/resources/libraries/glut/)  

调试说明
===
1. 下载<VStudio源工程>，解压。
2. VS代开根目录sln文件。
3. 确保freeglut.dll在Debug文件夹内。
4. 点击源.cpp即为主代码。

运行操作
===
1. 下载<标识-可执行程序> 或右侧Releases<_L.rar>。  
2. 解压后，dll、bmp*2、exe文件处于同一程序目录下。  
3. 点击exe运行。  
4. 将打开命令行窗口与绘制窗口，鼠标点击绘制窗口。
5. 命令行窗口有操作说明。  

操作说明
===
  1 - 重新生成5迭代树     2 - 打开网线    3 - 打开纹理         4 - 减少迭代次数     5 - 增加迭代次数  
  q - 返回初始机位                   e - 切换投影方式  
                     w - 前移  
  a - 左移           s - 后移        d - 右移  
  z - 空间顺时旋转                   c - 空间逆时旋转  
                     x - 关闭/打开网线  
  鼠标滚轮向上滚动 - 上移            鼠标滚轮向下滚动 - 下移  


效果
===
1. 旋转视角，无透视模式下查看分形树的整体。  
2. 按下e，切换至透视模式，w/s可前进后退查看分形树的细节。  
3. 大键盘3，打开/关闭纹理。  
4. 大键盘1，重新生成随机文法，对应分形树发送改变。  
5. 大键盘4/5，文法迭代深度，迭代深度越高，通常其分形树越复杂。  
   (因为文法元复杂度不同，连续使用复杂文法元导致浅层树可能较复杂)  
   **深度>8，可能导致性能下降**(因为迭代复杂规模n^2)  

代码说明
===
### TODO: CPP代码整理
核心代码在<code.cpp>
**一股脑**把bmp纹理加载代码+文法生成代码+渲染器绘制代码+输入交互代码放一起了。  
导致code.cpp比较冗杂。有时间分割掉。  
此处说明**文法生成**与**根据绘制分形树**  
其余代码(纹理加载、交互、glut框架)详见<code.cpp>  
或者源工程中的<源.cpp>  

### 思路
1. 使用了以前图形学的代码_读取bmp生成纹理。  
2. 利用分形的思想，并加入随机过程的影响，每次迭代分形时**随机选取预设分形策略**。  
3. 绘制前先根据分形思想，由初始字符串，根据文法生成元迭代字符串，然后根据字符串绘制分形树。
4. 文法生成规则如下:  
  (1). F绘制树干，X绘制叶子，AB更改树干参数，+-/*%&坐标变换，[]进出栈  
  (2). 如有文法FA[+*&X][-/X][+%X]B,则表示渲染器有如下操作  
       绘制树干->树干长度变化->坐标系入栈->坐标连续变换+*&->绘制叶子->坐标系出栈->坐标系入栈->...   
  (3). 文法终结于**叶子X**，按照**分形**与**数据结构_生成树**的思路，可以将**叶子X替换为另一段文法**，即将**叶子换为树干+叶子**，则这颗树"长大"了。太好了，好耶！  
  (4). 将叶子X替换为文法时，可以预设不同的文法生成元(类似(2)例子)，随机选择不同的生成元替换该文法。  
  (5). 由此，一段**简短**的初始文法将被**分形迭代**为复杂超超超超超级级级级级级级级长长长长长长长长长的文法。  
6. 遍历目标文法，switch判定此时渲染器操作。树生成完成，完美。  
7. 然后是一些简单的输入交互。

### 代码结构
>纹理加载  
>>判定2的整数次幂 power_of_two(int n) 用于LoadTexture()  
>>读取bmp文件加载纹理 LoadTexture(const char* file_name)  

>分形文法生成  
>>预设文法生成元  
>>根据初始元生成文法字符串 grammarIteration(string grammar, int level)  

>绘制分形树  
>>绘制树干 drawTree()  
>>绘制叶子 drawLeaf()  
>>根据文法绘制分形树 grammarDraw(string grammar)  

>glut回调  
>>绘制空间 在此处编写绘制代码 DrawRoom() 用于myDisplay()  
>>显示回调函数 myDisplay() 在main中被指定  
>>初始化函数 Initial()  

>交互回调  
>>键盘交互 keyBoard()  
>>鼠标交互 onMouseWheel()  

>主函数 main()  

### 文法生成代码
```C++
//文法生成元
//F绘制树干，X绘制叶子，AB更改树干参数，+-/*%&坐标变换，[]进出栈
string grammar1 = "FA[+*&X][-/X][+%X]B";
string grammar2 = "FA[%-X][&*X][+/X][-*X]B";
string grammar3 = "FA[-*X][/&X][*%X][+&X]B";
string treegra;

//根据元生成文法字符串
string grammarIteration(string grammar, int level)
{
	string tempRule = grammar;
	string rule = {};
	for (int i = 1; i <= level; i++) // 迭代level次，每次遍历字符串，将X替换为文法生成元
	{
		int curlen = tempRule.length();
		int j = 0;
		srand((int)time(0));//随机种子
		while (j < curlen) {
			if ('X' == tempRule[j]) { // X迭代，替换成文法模型		
				int n = random(3);
				//cout << n << endl;
				if (0 == n) {
					rule += grammar3;
				}
				else if (1 == n) {
					rule += grammar2;
				}
				else if (2 == n) {
					rule += grammar1;
				}
				j++;
			}
			else { // 保留	
				rule += tempRule[j];
				j++;
			}
		}
		tempRule = rule;
		rule.clear();
	}
	rule = tempRule;
	return rule;
}
```

### 着色器渲染代码
**仅渲染生成树部分，然后在绘制回调glutDisplayFunc(&func)的func()中调用grammarDraw(grammar)即可**  
```C++
// 绘制树干,用于grammarDraw()
void drawTree(float baseRadius, float topRadius, float height)
{
	GLUquadricObj *tree_obj;
	tree_obj = gluNewQuadric();//创建二次曲面对象
	if (tree != 0 && veinState == 1) {
		glBindTexture(GL_TEXTURE_2D, tree);//绑定纹理
		gluQuadricTexture(tree_obj, GL_TRUE);
	}
	else {
		glColor3ub(155, 81, 33);//纹理加载失败时代替
	}
	gluCylinder(tree_obj, baseRadius, topRadius, height, 30, 30);//绘制
	glTranslatef(0.0f, 0.0f, height);
	gluSphere(tree_obj, topRadius, 30, 30);
	gluDeleteQuadric(tree_obj);
}
// 绘制叶子,用于grammarDraw()
void drawLeaf()
{
	if (leaf != 0 && veinState == 1) {
		glEnable(GL_ALPHA_TEST);
		glAlphaFunc(GL_GREATER, 0.9f);
		glBindTexture(GL_TEXTURE_2D, leaf);//绑定纹理
		glBegin(GL_QUADS);
		glTexCoord2f(0.0, 0.0); glVertex3f(0, 0, 0);
		glTexCoord2f(1.0, 0.0); glVertex3f(10, 10, 0);
		glTexCoord2f(1.0, 1.0); glVertex3f(0, 20, 0);
		glTexCoord2f(0.0, 1.0); glVertex3f(-10, 10, 0);
		glEnd();
		glDisable(GL_ALPHA_TEST);
	}
	else {
		glColor3f(1.0f, 0.0f, 0.0f);//纹理加载失败时代替
		glBegin(GL_QUADS);
		glVertex3f(0, 0, 0);
		glVertex3f(5, 5, 0);
		glVertex3f(0, 10, 0);
		glVertex3f(-5, 5, 0);
		glEnd();
	}
}
// 根据文法绘制分形树
void grammarDraw(string grammar)
{
	float baseRadius = 10, topRadius = 6, height = 200;
	int i = 0;
	while (i < grammar.length()) {
		switch (grammar[i]) {
			//F绘制树干，X绘制叶子，AB更改树干参数，+-/*%&坐标变换，[]进出栈
		case'F':drawTree(baseRadius, topRadius, height); break;
		case'X':drawLeaf(); break;
		case'A':height *= 0.6; baseRadius *= 0.5; topRadius *= 0.5; break;
		case'B':height /= 0.6; baseRadius /= 0.5; topRadius /= 0.5; break;
		case'+':glRotatef(35, 1, 0, 0); break;
		case'-':glRotatef(-35, 1, 0, 0); break;
		case'*':glRotatef(35, 0, 1, 0); break;
		case'/':glRotatef(-35, 0, 1, 0); break;
		case'%':glRotatef(35, 0, 0, 1); break;
		case'&':glRotatef(-35, 0, 0, 1); break;
		case'[':glPushMatrix(); break;
		case']':glPopMatrix(); break;
		}
		i++;
	}
}
```

### 读取BMP生成纹理代码
```C++
//判断一个整数是不是2的整数次幂
int power_of_two(int n)
{
	if (n <= 0) {
		return 0;
	}
	return (n & (n - 1)) == 0;
}
//读取bmp文件加载纹理并返回
GLuint LoadTexture(const char* file_name)
{
	GLint width, height, total_bytes;
	GLubyte* pixels = 0;
	GLuint last_texture_ID = 0, texture_ID = 0;

	// 打开文件，如果失败，返回	
	FILE* pFile;
	if (fopen_s(&pFile, file_name, "rb")) {
		//printf_s("文件 %s 加载失败 无法打开\n", file_name);
		return 0;
	}

	// 读取文件中图象的宽度和高度	
	fseek(pFile, 0x0012, SEEK_SET);
	fread(&width, 4, 1, pFile);
	fread(&height, 4, 1, pFile);
	fseek(pFile, BMP_Header_Length, SEEK_SET);

	// 计算每行像素所占字节数，并根据此数据计算总像素字节数	
	GLint line_bytes = width * 3;
	while (line_bytes % 4 != 0) {
		++line_bytes;
	}
	total_bytes = line_bytes * height;

	// 根据总像素字节数分配内存
	pixels = (GLubyte*)malloc(total_bytes);
	if (0 == pixels) {
		fclose(pFile);
		printf_s("文件 %s 加载失败 像素为0\n", file_name);
		return 0;
	}

	// 读取像素数据	
	if (fread(pixels, total_bytes, 1, pFile) <= 0) {
		free(pixels);
		fclose(pFile);
		printf_s("文件 %s 加载失败 数据缺失\n", file_name);
		return 0;
	}

	// 对就旧版本的兼容，如果图象的宽度和高度不是的整数次方，则需要进行缩放
	// 若图像宽高超过了OpenGL规定的最大值，也缩放	
	GLint max;
	glGetIntegerv(GL_MAX_TEXTURE_SIZE, &max);
	if (!power_of_two(width)
		|| !power_of_two(height)
		|| width > max
		|| height > max)
	{
		const GLint new_width = 256;
		const GLint new_height = 256; // 规定缩放后新的大小为边长的正方形		
		GLint new_line_bytes, new_total_bytes;
		GLubyte* new_pixels = 0;

		// 计算每行需要的字节数和总字节数	
		new_line_bytes = new_width * 3;
		while (new_line_bytes % 4 != 0) {
			++new_line_bytes;
		}
		new_total_bytes = new_line_bytes * new_height;

		// 分配内存		
		new_pixels = (GLubyte*)malloc(new_total_bytes);
		if (0 == new_pixels) {
			free(pixels);
			fclose(pFile);
			printf_s("文件 %s 加载失败 内存分配失败\n", file_name);
			return 0;
		}

		// 进行像素缩放		
		gluScaleImage(GL_RGB,
			width, height, GL_UNSIGNED_BYTE, pixels,
			new_width, new_height, GL_UNSIGNED_BYTE, new_pixels);

		// 释放原来的像素数据，把pixels指向新的像素数据，并重新设置width和height		
		free(pixels);
		pixels = new_pixels;
		width = new_width;
		height = new_height;
	}

	//设置白色为透明的0,之前读取的RGB，现在需要RGBA
	unsigned char* texture = 0;
	texture = (unsigned char*)malloc(width * height * 4);
	for (int m = 0; m < width; m++) {
		for (int j = 0; j < height; j++) {
			//把颜色值写入   	
			texture[m*width * 4 + j * 4] = pixels[m*width * 3 + j * 3];
			texture[m*width * 4 + j * 4 + 1] = pixels[m*width * 3 + j * 3 + 1];
			texture[m*width * 4 + j * 4 + 2] = pixels[m*width * 3 + j * 3 + 2];

			//设置alpha值,假设白色为透明色   
			if (texture[m*width * 4 + j * 4] >= 200
				&& texture[m*width * 4 + j * 4 + 1] >= 200
				&& texture[m*width * 4 + j * 4 + 2] >= 200) {
				texture[m*width * 4 + j * 4 + 3] = 0;//透明，alpha=0  
				//cout << (int)texture[m*width * 4 + j * 4 + 3];
			}
			else {
				texture[m*width * 4 + j * 4 + 3] = 255;//不透明，alpha=255  
				//cout << (int)texture[m*width * 4 + j * 4 + 3];
			}
		}
	}

	// 分配一个新的纹理编号
	glGenTextures(1, &texture_ID);
	if (0 == texture_ID) {
		free(pixels);
		free(texture);
		fclose(pFile);
		printf_s("文件 %s 加载失败 纹理分配失败\n", file_name);
		return 0;
	}

	// 绑定新的纹理，载入纹理并设置纹理参数
	// 在绑定前，先获得原来绑定的纹理编号，以便在最后进行恢复	
	GLint lastTextureID = last_texture_ID;
	glGetIntegerv(GL_TEXTURE_BINDING_2D, &lastTextureID);
	glBindTexture(GL_TEXTURE_2D, texture_ID);
	glPixelStorei(GL_UNPACK_ALIGNMENT, 1);

	glTexParameterf(GL_TEXTURE_2D, GL_TEXTURE_WRAP_S, GL_CLAMP);
	glTexParameterf(GL_TEXTURE_2D, GL_TEXTURE_WRAP_T, GL_CLAMP);
	glTexParameterf(GL_TEXTURE_2D, GL_TEXTURE_MAG_FILTER, GL_NEAREST);
	glTexParameterf(GL_TEXTURE_2D, GL_TEXTURE_MIN_FILTER, GL_NEAREST);

	glTexImage2D(GL_TEXTURE_2D, 0, 4, width, height, 0, GL_RGBA, GL_UNSIGNED_BYTE, texture);
	gluBuild2DMipmaps(GL_TEXTURE_2D, 4, width, height, GL_BGRA_EXT, GL_UNSIGNED_BYTE, texture);
	glTexEnvf(GL_TEXTURE_ENV, GL_TEXTURE_ENV_MODE, GL_MODULATE);

	glBindTexture(GL_TEXTURE_2D, lastTextureID);  //恢复之前的纹理绑定
	free(pixels);
	free(texture);
	//printf_s("文件 %s 纹理加载 成功\n", file_name);
	return texture_ID;
}
```

许可
===
本项目使用 Apache 2.0 license.<br>
更多关于**许可**和**致谢**, 请参见 [LICENSE](LICENSE).
