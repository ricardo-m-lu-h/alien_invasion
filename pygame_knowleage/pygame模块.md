# Pygame库《外星入侵》

[TOC]



## 规划项目

**若只需要项目完整代码，请至文档末尾的项目完整代码查看。**

* 游戏为射击游戏，玩家控制最初出现在屏幕底部中央的飞船。
* 玩家使用箭头键左右移动飞船，空格键进行射击。
* 游戏开始外星人出现在天空，在屏幕中向下移动。
* 玩家任务是射杀外星人，当玩家将所有外星人消灭殆尽后，将出现一群新的速度更快的外星人。
* 当有外星人撞到飞船或到达屏幕底部，玩家就损失一艘飞船。
* 当玩家损失三艘飞船后，游戏结束。

第一开发阶段，创建一艘可左右移动的飞船，且按下空格键可以发射子弹。设置好后，便可以进行外星人的设计，并提高游戏的可玩性。







## 武装飞船（响应输出、绘制飞船、重构模块、驾驶飞船、添加武器）

### 一、响应输出
#### 1.创建Pygame窗口以及响应用户输入

模块 pygame 包含开发游戏所需的功能，建立一个空的 pygame 窗口，代码如下：

**alien_invasion.py**

```python
import sys
import pygame

#建立游戏屏幕函数
def run_game():
    #初始化游戏，并创建一个屏幕对象
	pygame.init()
    screen = pygame.display.set_mode((1200, 800))
    pygame.display.set_caption("Alien Invasion")

	#开始游戏的主循环
    while True:
	
    	#监视键盘和鼠标事件
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                sys.exit()
                
        #让最近绘制的屏幕可见
        pygame.display.flip()
        
run_game()

```

首先，我们导入了模块 sys 和 pygame。模块 pygame 用于开发游戏所需功能；玩家退出时，使用模块 sys 中的 sys.exit() 来退出游戏。

* 游戏开头的函数 run_game() 中，第7行的代码行 pygame.init() 初始化背景设置，让 pygame 能正确的工作。

  

* 第8行代码行，调用 pygame.display.set_mode(()) 来创建一个 screen 的显示窗口（显示 Surface），这个游戏的所有图形都将在其中绘制。实参尺寸需要多写一个括号，否则会报错。**pygame.display 模块**在 pygame 中用于控制窗口和屏幕显示的模块。

  实参（1200， 800）是一个元组，指定了游戏窗口的尺寸。通过传递尺寸值传递给 pygame.display.set_mode()。

  

* 第9行代码行，调用 pygame.display.set_caption("名称")，设置屏幕的名称。

  

* 第12行代码行，使用 while 循环控制，其中包含一个事件循环以及管理屏幕更新的代码。事件就是用户的执行操作，如按键或移动鼠标。

  * 第17-19行代码行，使用 for 循环检测键盘和鼠标的操作，所有的键盘和鼠标操作都将促使 for 循环运行。
    * 在此循环中，使用 if 语句检测事件是否为窗口关闭按钮，当 pygame.QUIT 事件被响应，调用 sys.exit() 来退出游戏。



* 第22行代码行，调用了 pygame.display.flip()，命令 pygame 让最近绘制的屏幕可见。随着 while 循环命令，每次都绘制一个空屏幕，并擦去旧屏幕，使得只有旧屏幕可见。

> **注意**：所有绘制代码，都应该在 pygame.display.flip() 之前，否则无法绘制。

  

* 最后一行， 调用 run_game()，将初始化游戏，并开始主循环。运行代码后，会看到一个空的 Pygame 窗口。



![1678585419253](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1678585419253.png)





#### 2.设置背景色

pygame 默认创建一个黑色屏幕，也可更改背景颜色：

**alien_invasion.py**

```python
--snip--
def run_game():
    --snip--
    pygame.display.set_caption("Alien Invasion")
    
    #设置背景色
    bg_color = (230, 230, 230)
    
    #开始游戏主循环
    while True:
        
        #监听键盘和鼠标事件
        --snip--
        
        #每次循环时都重绘屏幕
        screen.fill(bg_color)
        
        #让最近绘制的屏幕可见
        pygame.display.flip()
        
run_game()

```

在 pygame 中，颜色是以 RGB 值指定的，这种颜色由红色、绿色和蓝色值组成，每个值的可能取值范围都为0~255.颜色值（255，0，0）表示红色，（0，255，0）表示绿色，（0，0，255）表示蓝色。

* 第7行代码行，设置了背景色并将其存储在 bg_color 中，因此在进入主 while 循环前定义，该颜色只需指定一次。



* 第16行代码行，调用了方法 screen.fill()，用背景色填充屏幕，这个方法只接受一个实参：一种颜色。



![1678585609990](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1678585609990.png)





#### 3.创建设置类

给游戏添加新功能，通常也将引入一些新设置。将所有设置编写在新的模块，避免在游戏代码中到处添加设置。使得传递的是一个设置对象，且项目增大时修改游戏外观更容易，修改设置只需修改 setting.py 中的值。

**setting.py**

```python
class Settings():
    """存储游戏的所有设置"""
    
    def __init__(self):
    	#屏幕设置
    	self.screen_width = 1200
    	self.screen_height = 800
    	self.bg_color = (230, 230, 230)
    
```

创建 Settings 实例并使用它来访问设置，将 alien_invasion.py 修改为：

**alien_invasion.py**

```python
--snip--
import pygame

from settings import Settings

def run_game():
    #初始化pygame、设置和屏幕对象
    pygame.init()
    ai_settings = Settings()
    screen = pygmae.display.set_mode(
    	(ai_settings.screen_width, ai_settings.screen_height))
    pygame.display.set_vaption("Alien Invasion")
    
    #开始游戏主循环
    while True:
        --snip--
        
        #每次循环时都重绘屏幕
        screen.fill(ai_settings.bg_color)
        
        #让最近绘制的屏幕可见
        pygame.display.flip()
        
run_game()

```

* 在 Settings.py 中建立了一个 settings 类，且类中无函数，仅有类的属性。

  

* 在 alien_invasion.py 中调用 pygame.init() ，创建了一个 Settings实例，并将其存储在变量 ai_settings() 中。



* 第10行代码行，调用了 ai_settings 的属性 screen_width 和 screen_height；以及第19行代码行，使用了 ai_settings 的属性 bg_color。








### 二、绘制飞船
使用免费图像网站下载图形，需要加载一幅图像，再使用 pygame 方法 blit() 绘制玩家的飞船。

在游戏中机会可以使用任何类型的图像文件，但使用位图（.bmp）文件最为简单，因 pygame 默认加载位图。虽可配置 pygame 以使用其他文件类型，但有些文件类型要求在计算机上安装相应的图像库。大多数图像为 .jpg、.png 或 .gif 格式，可使用 Photoshop 等工具转换。

选择图像时，要特别注意背景色。尽可能选择背景透明的图像，就可使用图像编辑器将其背景设置为任何颜色。当图像的背景色存在背景色，则设置背景颜色相同为最佳。

设置一个新的文件夹 images，存入图 像文件。

#### 1.添加飞船图像

选择用于飞船的图像，需要显示于屏幕上。创建一个 ship 模块，编写负责管理飞船的大部分行为。

**ship.py**

```python
import pygame

class Ship():
    
    def __init__(self, screen):
        """初始化飞船并设置其初始位置"""
        self.screen = screen
        
        #加载飞船图像并获取其外接矩形
        self.image = pygame.image.load('images/ship.bmp')
        self.rect = self.image.get_rect()
        self.screen_rect = screen.get_rect()
        
        #将每艘新飞船放在屏幕底部中央
        self.rect.centerx = self.screen_rect.centerx
        self.rect.bottom = self.screen_rect.bottom
        
    def blitme(self):
        """在指定位置绘制飞船"""
        self.screen.blit(self.image, self.rect)
        
```

首先，导入模块 pygame 。Ship 的方法 __ init__() 接受两个参数：引用 self 和 screen，**self 为前缀的变量可供所有方法使用**，其后者指定了将飞船绘制到什么位置。

* 第10行代码行，使用 pygame.image.load() 加载图像。此函数返回表示飞船的 surface，将其存储到 self.image 中。



* 第11行代码行，加载图像后，使用 get_rect() 获取相应 surface 的属性 rect。
  * pygame 的效率之所以高，一个原因是它让你能够像处理矩阵（rect 对象）一样处理游戏元素，即使他们的形状并非矩形。
  * 像处理矩形一样处理游戏元素之所以高效，是因为矩形是简单几何形状。这种效果通常很好，几乎注意补发哦处理的不是游戏对象的实际形状。
  * 处理 rect 对象时，可使用矩形四角和中心的 x 和 y 坐标。通过设置这些值指定矩形位置。



* 要将游戏元素居中，可设置相应 rect 对象的属性矩形中央 center、centerx 或 centery。

  * 要让游戏元素与屏幕边缘对齐，可使用属性 top、bottom、left 或 right，矩形高度和宽度 width 和 height。
  * 调整游戏元素水平或垂直位置，可使用属性 x 和 y，分别时相应矩形下边缘和上边缘的 x 和 y 坐标。

> **注意**：在 pygame 中，原点（0， 0）位于屏幕左上角，向右下方移动时，坐标值将增大。在1200x800的屏幕上，原点位于左上角，而右下角的坐标为（1200，800）。



* 第12行代码行，首先使用方法 screen.get_rect() 将屏幕矩形存储在 self.screen_rect 中。

  

* 第15、16行代码行，再将 self.rect.centerx（飞船中的 x 坐标）设置为表示屏幕的矩形的属性 centerx，并将 self.rect.bottom（飞船下边缘的 y 坐标）设置为表示屏幕的矩形的属性 bottom。pygame 将使用 rect 属性放置飞船图像，使其与屏幕下边缘对齐并水平居中。



* 第19、20行代码行，定义了方法 blitme()，根据 self.rect 指定的位置将图像绘制到屏幕上pygame.surface.blit( ) 方法旨在将一个图像绘制到另一个图像的上方，其主要格式：screen.blit(image, rect)





#### 2.在屏幕上绘制飞船

接下来更新 alien_invasion.py，使其创建一艘飞船，并调用其方法 blitme()：

**alien_invasion.py**

```python
--snip--
from settings import Settings
from ship import Ship

def run_game():
    --snip--
    pygame.display.set_caption("Alien Invasion")
    
    #创建一艘飞船
    ship = Ship(screen)
    
    #开始游戏主循环
    while True:
        --snip--
        #每次循环时都重绘屏幕
        screen.fill(ai_settings.bg_color)
        ship.blitme()
        
        #让最近绘制的屏幕可见
        pygame.display.flip()
        
run_game()
    
```

* 导入 Ship 类，并在创建屏幕后创建一个 ship 的 Ship 实例。必须在主循环 while 循环前面创建该实例，以免每次循环时都创建时都创建一艘飞船。



* 填充背景后，调用 ship.blitme() 将飞船绘制到屏幕上，确保飞船出现在背景前面。



![1678585885655](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1678585885655.png)







### 三、重构模块：game_functions

大型项目中，经常需要在添加新代码前重构既有代码。重构旨在简化既有代码的结构，使其更容易拓展。通过创建 game_functions 的新模块，用于存储大量让游戏运行的函数。通过创建模块 game_functions，可避免 alien_invasion.py 太长，并使其逻辑更容易理解。

#### 1.函数 check_events()

首先把管理事件的代码移到 check_events() 的函数中，以简化 run_game() 并隔离事件管理循环。通过隔离事件循环，可将事件管理与游戏的其他方面（如更新屏幕）分离。

将 check_events() 其放入 game_functions 的模块中：

**game_functions.py**

```python
import sys
import pygame

def check_events():
    """响应按键和鼠标事件"""
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()

```

* 模块中导入了事件循环要使用的 sys 和 pygame。函数 check_events() 不需要任何形参，其函数体复制了 alien_invasion.py 的事件循环。



由此可修改 alien_invasion.py，使其导入模块 game_functions，并将事件循环替换为对函数 check_events() 的调用：

**alien_invasion.py**

```python
import pygame

from settings import Settings
from ship import Ship
import game_functions as gf

def run_game():
    --snip--
    #开始游戏主循环
    while True:
        gf.check_events()
        
        #让最近绘制的屏幕可见
        --snip--

```

* 因在模块 game_functions 中调用了 sys ，在主程序文件中不再需要直接导入。出于简化目的，给导入模块 game_functions 指定了别名 gf。





#### 2.函数 update_screen()

为进一步简化 run_game()，下面将更新屏幕的代码移到 update_screen() 的函数中，并将函数放在模块 game_functions.py 中：

**game_functions.py**

```python
--snip--

def check_events():
    --snip--
    
def update_screen(ai_settings, screen, ship):
    """更新屏幕上的图像，并切换到新屏幕"""
    #每次循环时都重绘屏幕
    screen.fill(ai_settings.bg_color)
    ship.blitme()
    
    #让最近绘制的屏幕可见
    pygame.display.flip()

```

* 新函数 update_screen() 包含三个形参： ai_settings、 screen 和 ship。



现在需 alien_invasion.py 的 while 循环中更新屏幕的代码替换为对函数 update_screen() 的调用：

**alien_invasion.py**

```python
--snip--
	#开始游戏主循环
    while True:
        gf.check_events()
        gf.update_screen(ai_settings, screen, ship)
        
run_game()

```

* 这两个函数让 while 循环更简单，并让后续开发更容易：在模块 game_functions 而不是 run_game() 中完成大部分工作。



* 了解实际开发过程：一开始将代码编写的尽可能简单，并在项目越来越复杂时进行重构。

对代码进行重构使其更容易拓展后，可以开始处理游戏的动态部分。







### 四、驾驶飞船

在屏幕中，能够左右移动飞船。编写代码，在用户按左或右箭头键时作出响应。

#### 1.响应按键

每当用户按键时，都将在 pygame 中注册一个事件。事件通过方法 pygame.event.get() 获取的，因此在函数 check_events() 中，需指定检查哪些类型事件。每次按键都被注册为一个 KEYDOWN 事件。

检测到 KEYDOWN 事件时，需检查按下的是否为特定的键。例如，若按下为右箭头键，增大飞船的 rect.centerx 值，将飞船向右移动。

**game_functions.py**

```python
def check_events(ship):
    """响应按键和鼠标事件"""
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
            
    	elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_RIGHT:
                #向右移动飞船
                ship.rect.centerx += 1

```

* 在函数 check_events() 中包含形参 ship，因当玩家按右箭头键时，需要将飞船向右移动，屏幕的左上角为坐标（0，0）。



* 在函数 check_events() 内部，事件循环中添加了 elif 代码块，以便在 pygame 检测 KEYDOWN事件时做出响应。



* 读取属性 event.key，以检查按下的是否为右箭头键（pygame.K_RIGHT）。若按下的是右箭头键，就将 ship.rect.centerx 的值加1，从而将飞船向右移动。



在 alien_invasion.py 中，需更新调用 check_events() 代码，将 ship 作为实参传递入代码中：

**alien_invasion.py**

```python
	#开始游戏主循环
	while True:
        gf.check_events(ship)
        gf.update_screen(ai_settings, screen, ship)

```

***目前代码运行，则每按右箭头一次，飞船都将向右移动1像素，且需要每次都按下放开按键才能进行移动。***但并非控制飞船的高效方式，，改进控制方式，允许持续移动。





#### 2.允许不断移动

玩家按住右箭头不放，飞船不断的向右移动，直到玩家松开为止。将让游戏检测 pygame.KEYUP 事件，以便玩家松开右箭头键可以检测到；然后，将结合使用 KEYDOWN 和 KEYUP 事件，以及建立 moving_right 的标志来实现持续移动。

飞船不动时，标志 moving_right 将为 False。玩家按下右箭头键时，将这个标志设置为 True ；而玩家松开时，这个标志重新设置为 False。

飞船的属性都由 Ship 类控制，因此将给这个类添加一个 moving_right 的属性和一个为 update() 的方法。方法 update() 检查标志 moving_right 的状态，如果这个标志为 True，就调整飞船的位置。每当需要调整飞船的位置时，都调用这个方法。

**ship.py**

```python
class Ship():
    
    def __init__(self, screen):
        --snip--
        #将每艘新飞船放在屏幕底部中央
        self.rect.centerx = self.screen_rect.centerx
        self.rect.bottom = self.screen_rect.bottom
        
        #移动标志
        self.moving_right = False
        
    def update(self):
        """根据移动标志调整飞船的位置"""
        if self.moving_right:
            self.rect.centerx += 1

    def blitme(self):
        --snip--

```

* 在方法 __ init __() 中，添加了属性 self.moving_right，并将其初始值设置为 False。然后，添加了方法 update()，在前述标志为 True 时向右移动飞船。



下面来修改 check_events()，使其在玩家按下右箭头键时将 moving_right 设置为 True，并在玩家松开时将 moving_right 设置为 False：

**game_functions.py**

```python
def check_events(ship):
    """响应按键和鼠标事件"""
    for event in pygame.event.get():
        --snip--
        """elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_RIGHT:
                #向右移动飞船
                ship.rect.centerx += 1
           被替换为以下代码"""
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_RIGHT:
                ship.moving_right = True
                
		elif event.type == pygame.KEYUP:
            if event.key == pygame.K_RIGHT:
                ship.moving_right = False

```

* 第7行代码行，修改了游戏在玩家按下右箭头键时响应的方式：不直接调整飞船的位置，而只是将 moving_right 设置为 True，通过条件语句进行飞船位置的移动。



* 第9-11行代码行，添加了一个新的 elif 代码块，用于响应 KEYUP 事件：玩家松开右箭头键（K_RIGHT）时，将 moving_right 设置为 False。



最后，需要修改 alien_invasion.py 中的 while 循环，以便每次执行循环时都调用飞船的方法 update()：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ship)
        ship.update()
        gf.update_screen(ai_settings, screen, ship)

```

***飞船的位置将在检测到键盘事件后（但在更新屏幕前）更新，只需要按下按键，飞船将会跟随移动，因为电脑检测键盘事件速度很快，所以飞船也相应迅速且流畅移动。***由此，当玩家输入时，飞船位置将更新，从而确保使用更新后的位置将飞船绘制到屏幕上。





#### 3.左右移动

飞船依据向右移动的逻辑，添加向左移动的逻辑。我们将再次修改 Ship 类和函数 check_events()。下面显示了对 Ship 类的方法 __ init __() 和 update() 所做的相关修改：

**ship.py**

```python
def __init__(self, screen):
    --snip--
    #移动标志
    self.moving_right = False
    self.moving_left = False
    
def update(self):
    """根据移动标志调整飞船的位置"""
    if self.moving_right:
        self.rect.centerx += 1
    if self.moving_left:
        self.rect.centerx -= 1

```

* 在方法 __ init __() 中，我们添加了标志 self.moving_left；
  * 在方法 update() 中，添加了一个 if 代码块而不是 elif 代码块，这样如果玩家同时按下了左右箭头键，将先增大飞船的 rect.centerx 值，再降低这个值，即飞船位置保持不变。
  * 如果使用 elif 代码块来处理向左移动的情况，右箭头键始终处于优先地位。从向左移动切换到向右移动时，玩家可能同时按照左右箭头键，由此，前面的做法让移动更准确。



则还需对 check_events() 作两方面的调整：

**game_functions.py**

```python
def check_events(ship):
    """响应按键和鼠标事件"""
    for event in pygame.event.get():
        --snip--
        elif event.type == pygame.KEYDOWN:
            if event.key == pygame.K_RIGHT:
                ship.moving_right = True
            elif event.key == pygame.K_LEFT:
                ship.moving_left = True
                
        elif event.type == pygame.KEYUP:
            if event.key == pygeme.K_RIGHT:
                ship.moving_right = False
            elif event.key == pygame.K_LEFT:
                ship.moving_left = False

```

* 若因玩家按下 K_LEFT 键而触发了 KEYDOWN 事件，我们就将 moving_left 设置为 True；
  * 若因玩家松开 K_LEFT 而触发了 KEYUP 事件，将 moving_left 设置为 False。
  * 之所以使用 elif 代码块，因为每个事件都只与一个键相关联；如果玩家同时按下了左右箭头键，将检测到两个不同事件。



* 若此时运行 alien_invasion.py，将能够不断的左右移动飞船；若同时按左右箭头键，飞船纹丝不动。





#### 4.调整飞船速度

目前，每次执行 while 循环时，飞船最多移动1像素，但我们可以在 Settings 类中添加属性 ship_speed_factor，用于控制飞船的速度。将根据此属性决定飞船每次循环时最多移动多少距离。

**settings.py**

```python
class Settings():
    """一个存储游戏的所有设置的类"""
    
    def __init__(self):
        --snip--
        
        #飞船的设置
        self.ship_speed_factor = 1.5

```

* 通过将速度设置指定为小数值，初始值为1.5，可在后面加快游戏的节奏时更细致的控制飞船的速度。



但 rect（矩形）的 centerx 等属性只能存储整数值，因此需要对 Ship 类做些修改：

**ship.py**

```python
class Ship():
    
    def __init__(self, ai_settings, screen):
        """初始化飞船并设置其初始位置"""
        self.screen = screen
        self.ai_settings = ai_settings
        --snip--
        
        #将每艘新飞船放在屏幕底部中央
        --snip--
        
        #在飞船的属性 center 中存储小数值
        self.center = float(self.rect.centerx)
        
        #移动标志
        self.moving_right = False
        self.moving_left = False
        
     def update(self):
        """根据移动标志调整飞船的位置"""
        if self.moving_right:
            self.center += self.ai_settings.ship_speed_factor
        if self.moving_left:
            self.center -= self.ai_settings.ship_speed_factor
            
        #根据self.center更新rect对象
        self.rect.centerx = self.center
        
    def blitme(self):
        --snip--

```

* 第3行代码行，在 __ init __() 的形参列表中添加了 ai_settings，让飞船能获取其速度设置。接下来，将形参 ai_settings 的值存储在一个属性中，以便能够在 update() 中使用它。



* 鉴于现在调整飞船的位置时，将增加或减去一个单位为像素的小数值，因此需要将位置存储小数值的变量中。可以使用小数来设置 rect 的属性，但 rect 将只存储这个值的整数部分。



* 第13行代码行，在飞船的属性 center 中存储小数值。为准确的存储飞船的位置，定义了一个可存储小数值的新属性 self.center。使用函数 float() 将 self.rect.centerx 的值转换为小数，并将结果存储到 self.center 中。

  

* 第22、24行代码行，将 self.rect.centerx 换写成 self.center。现在在 update() 中调整飞船的位置时，将 self.center 的值增加或减去 ai.settings.ship_speed_factor 的值。更新 self.center 后，在根据它来更新控制飞船位置的 self.rect.centerx。



* self.rect.centerx 将只存储 self.center 的整数部分，*但对显示飞船而言，这问题不大*。



在 alien_invasion.py 中创建 Ship 实例时，需要传入实参 ai_settings：

**alien_invasion.py**

```python
--snip--
def run_game():
    --snip--
    #创建飞船
    ship = Ship(ai_settings, screen)
    --snip--

```

现在，只要 ship_speed_factor 的值大于1，飞船的移动速度就会比以前更快。





#### 5.限制飞船的活动范围

当前，若玩家按住箭头键的时间足够长，飞船将移到屏幕外面。为修复这个问题，让飞船到达屏幕边缘后停止移动。为此，将修改 Ship 类的方法 update()：

**ship.py**

```python
def update(self):
    """根据移动标志调整飞船的位置"""
    #更新飞船的 center 值，而不是 rect
    if self.moving_right and self.rect.right < self.screen_rect.right:
        self.center += self.ai_settings.ship_speed_factor
    if self.moving_left and self.rect.left > 0:
        self.center -= self.ai_settings.ship_speed_factor
        
    #根据 self.center 更新 rect 对象
    self.rect.centerx = self.center

```

* 代码在修改 self.center 的值之前检查飞船的位置。self.rect.right 返回飞船外接矩形的右边缘的 x 坐标，若该值小于 self.screen_rect.right 的值，就说明飞船未触及屏幕右边缘。



* 左边缘的情况与右边类似：若 rect 的左边缘的 x 坐标大于零，就说明飞船未触及屏幕左边缘。

  这确保仅当飞船在屏幕内时，才能调整 self.center 的值。



![1678586430559](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1678586430559.png)







#### 6.重构 check_events()

随着游戏开发的进行，函数 check_events() 将越来越长，将其部分代码放两个函数中：一个处理 KEYDOWN 事件，另一个处理 KEYUP 事件：

**game_functions.py**

```python
def check_keydown_events(event, ship):
    """响应按键"""
    if event.key == pygame.K_RIGHT:
        ship.moving_right = True
    elif event.key == pygame.K_LEFT:
        ship.moving_left = True
        
def check_keyup_events(event, ship):
    """响应松开"""
    if event.key == pygame.K_RIGHT:
        ship.moving_right = False
    elif event.key == pygame.K_LEFT:
        ship.moving_left = False
        
def check_events(ship):
    """响应按键和鼠标事件"""
    for event in pygame.event.get():
    	if event.type == pygame.QUIT:
        	sys.exit()
    	elif event.type == pygame.KEYDOWN:
        	check_keydown_events(event, ship)
     	elif event.type == pygame.KEYUP:
        	check_keyup_events(event, ship)

```

* 创建了两个函数：check_keydown_events() 和 check_keyup_events()，都包含形参 event 和 ship。event 从事件列表中获取鼠标和键盘事件，ship 从 alien_invasion.py 中获取实例参数。



* 两个函数由 check_events() 中复制而来，因此将函数 check_events 中相应的代码替换成对这两个函数的调用。







### 五、添加武器

添加射击功能。编写玩家按空格键发射子弹（小矩形）的代码。子弹将在屏幕中向上穿行，抵达屏幕上边缘后消失。

#### 1.添加子弹设置

首先，更新 settings.py，在其方法 __ init() __() 末尾存储新类 Bullet 所需的值：

**settings.py**

```python
	def __init__(self):
        --snip--
        #子弹设置
        self.bullet_speed_factor = 1
        self.bullet_width = 3
        self.bullet_height = 15
        self.bullet_color = 60, 60, 60

```

设置创建了宽3像素、高15像素的深灰色子弹。子弹速度比飞船稍低。





#### 2.创建 Bullet 类

创建存储 Bullet 类的文件 bullet.py，前半部分编写设置子弹矩形属性：

**bullet.py**

```python
import pygame
from pygame.sprite import Sprite

class Bullet(Sprite):
    """一个对飞船发射的子弹进行管理的类"""
    
    def __init__(self, ai_settings, screen, ship):
        """在飞船所处的位置创建一个子弹对象"""
        super(Bullet, self).__init__()
        self.screen = screen
        
        #在（0，0）处创建一个表示子弹的矩形，再设置正确的位置
        self.rect = pygame.Rect(0, 0, ai_settings.bullet_width,
                               ai_settings.bullet_height)
        self.rect.centerx = ship.rect.centerx
        self.rect.top = ship.rect.top
        
    	#存储用小数表示的子弹位置
        self.y = float(self.rect.y)
        
        self.color = ai_settings.bullet_color
        self.speed_factor = ai_settings.bullet_speed_factor

```

* Bullet 类继承了从模块 pygame.sprite 中导入了 Sprite 类。通过使用精灵，可将游戏中相关的元素编组，进而同时操作编组中的所有元素。包含碰撞检测功能，感知时间流逝等。

 

* 为创建子弹实例，需要向 __ init __() 传递 ai_settings、 screen 和 ship 实例，还调用了

  super() 用于继承 Sprite 类。

>**注意**：代码 super(Bullet, self).__ init __ () 使用了 python2.7 语法。这种语法同样适用于 python3，但可将其代码简写为 super(). __ init __()。



* 第13行代码行，创建了子弹属性 rect。子弹并不基于图像，因此必须使用 pygame.Rect() 类从空白开始创建一个矩形。
  * 创建这个类的实例时，必须提供矩形左上角的 x 坐标和 y 坐标，还有矩形的宽度和高度。
  * 在（0，0）处创建这个矩形，但接下来的15、16行代码将其移到了正确位置，因为子弹的初始位置取决于飞船当前的位置。
  * 子弹的宽度和高度，是从run_game模块中的 ai_settings 中获取的。



* 第15、16行代码行，将子弹的 centerx 设置为飞船的 rect.centerx。子弹应从飞船顶部射出，因此将表示子弹的 rect 的 top 属性设置为飞船的 rect 的 top 属性。



* 第19行代码行，将子弹的 y 坐标存储为小数值，以便于能够微调子弹的速度。

  

* 第21、22行代码行，将子弹的颜色和速度分别存储到 self.color 和 self.speed_factor 中。



下面是 bullet.py 的下半部分，进行子弹位置的移动更新和绘制——方法 update() 和 draw_bullet()：

**bullet.py**

```python
	def update(self):
        """向上移动子弹"""
        #更新表示子弹位置的小数值
        self.y -= self.speed_factor
        #更新表示子弹的 rect 的位置
        self.rect.y = self.y
        
    def draw_bullet(self):
        """在屏幕上绘制子弹"""
        pygame.draw.rect(self.screen, self.color, self.rect)

```

* 方法 update() 管理子弹的位置。发射后，子弹在屏幕中向上移动，意味着 y 坐标将不断减小，因此为更新子弹的位置，从 self.y 中减去 self.speed_factor 的值。

  方法 update() 和 draw_bullet() 须写在类 Bullet 下，即注意缩进。



* 接下来，将 self.rect.y 设置为 self.y 的值，将子弹的位置随 self.y 而变化，即位置更新。



* 属性 speed_factor 能够随着游戏的进行或根据需要提高子弹的速度，以调增游戏的行为。子弹发射后，其 x 坐标始终不变，因此子弹将沿直线垂直的往上穿行。



* 绘制子弹，调用 draw.rect() 函数，使用存储在 self.color 中的颜色填充表示子弹的 rect 占据的屏幕部分。





#### 3.将子弹存储到编组中

定义 Bullet 类和必要的设置后，就可以编写代码了，在玩家每次按空格键时都射出一发子弹。

首先，将在 alien_invasion.py 中创建一个编组（group），用于存储所有有效的子弹。

该编组将是 pygame.sprite.Group 类的一个实例；pygame.sprite.Group 类类似于列表，但提供了有助于开发游戏的额外功能。

在主循环中，将使用这个编组在屏幕上绘制子弹，以及更新每颗子弹的位置：

**alien_invasion.py**

````python
import pygame
from pygame.sprite import Group
--snip--

def run_game():
    --snip--
    #创建一艘飞船
    ship = Ship(ai_settings, screen)
    #创建一个用于存储子弹的编组
    bullets = Group()
    
    #开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        bullets.update()
        gf.update_screen(ai_settings, screen, ship, bullets)
        
run_game()

````

* 导入了 pygame.sprite 中的 Group 类。第10行代码行，创建了一个 Group 实例，并将其命名为 bullets。该代码是在 while 循环外面创建的，这样就无需运行该循环时都创建一个新的子弹编组。

>**注意**：如果在循环内创建这要的编组，游戏运行时创建数千个子弹编组，导致游戏运行速度缓慢。若游戏停滞不前，请仔细检查主 while 循环中发生的情况。



* 将 bullets 传递给了 check_events() 和 update_screen()。在 check_events() 中，需要在玩家按空格键时处理 bullets；而在 update_screen() 中，需要更新要绘制到屏幕上的 bullets。



当对编组调用 update() 时，编组将自动对其中的每个精灵调用 update() ，因此代码行 bullets.update() 将为编组 bullets 中的每颗子弹调用 bullet.update()。





#### 4.开火

在 game_functions.py 中，需要修改 check_keydown_events()，以便在按下空格键时发射一颗子弹。无需修改 check_kedown_events()，因为当松开空格键时，不会有什么变化。还需要修改 update_screen()，确保在调用 filp() 前在屏幕上重绘每颗子弹。

对 game_functions.py 所对的相关修改：

**game_functions.py**

```python
--snip--
from bullet import Bullet

def check_keydown_events(event, ai_settings, screen, ship, bullets):
    --snip--
    elif event.key == pygame.K_SPACE:
        #创建一颗子弹，并将其加入到编组 bullets 中
        new_bullet = Bullet(ai_settings, screen, ship)
        bullets.add(new_bullet)
        --snip--
        
def check_events(ai_settings, screen, ship, bullets):
    """响应按键和鼠标事件"""
    for event in pygame.event.get():
        --snip--
        elif event.type == pygame.KEYDOWN:
           	#check_keydown_events(event, ship) 修改为以下代码
            check_keydown_events(event, ai_settings, screen, ship, bullets)
        --snip
            
def update_screen(ai_settings, screen, ship, bullets):
    #每次循环时都重绘屏幕
    screen.fill(ai_settings.bg_color)
    #在飞船和外星人后面重绘所有子弹
    for bullet in bullets.sprites():
        bullet.draw_bullet()
	--snip--

```

* 编组 bullets 传递给了 check_keydown_events()（第4行）。玩家按空格键时，创建一颗新子弹（一个名为 new_bullet 的 Bullet 实例），并使用方法 add() 将其加入编组 bullets 中（第6行）。



* 在 check_events() 的定义中，需要添加形参 bullets（第12行）；调用 check_keydown_events() 时，需要将 bullets 作为实参传递给它。



* 给屏幕上绘制子弹的 update_screen() 添加了形参 bullets（第20行）。方法 bullets.sprites() 返回一个列表，其中包含编组 bullets 中的所有的精灵。



* 为在屏幕上绘制发射的所有子弹，遍历编组 bullets 中的精灵，并对每个精灵都调用 draw_bullet()（第23行）。



![1678590558014](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1678590558014.png)





#### 5.删除已消失的子弹

子弹抵达屏幕顶端后消失，仅因 pygame 无法在屏幕外面绘制子弹。但在计算机中实际上依旧存在，子弹的 y 坐标为负数，且越来越小。子弹存在，将继续消耗内存和处理能力。

需要将屏幕上无法绘制的子弹删除，否则游戏所做的无谓工作越来越多，进而变得越来越慢。为此，需要检测这样的条件，即表示子弹的 rect 的 bottom 属性为零，表明子弹已穿过屏幕顶端：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        bullets.update()
        
        #删除已消失的子弹
        for bullet in bullets.copy():
            if bullet.rect.bottom <= 0:
                bullets.remove(bullet)
        print(len(bullets))
        
        gf.update_screen(ai_settings, screen, ship, bullets)

```

* 在 for 循环中，不应从列表或编组中删除条目，因此必须遍历编组的副本。使用方法 copy() 来设置 for 循环（第8行代码），使得能够修改 bullets。



* 检查每颗子弹，若已从屏幕顶端消失（第9行代码）。若消失，就将其从 bullets 中删除（第10行代码）。



* 第11行代码行，使用 print 语句，以显示当前还有多少颗子弹，从而核实已消失的子弹确实删除了。



* 若代码没有问题，发射子弹后查看终端窗口时，将发现随着子弹一颗颗的在屏幕顶端消失，子弹数逐渐降为零。运行没有问题后，将 print 语句删除。留下会使得游戏速度大大降低，因将输出写入到终端而话费的时间比将图形绘制到游戏窗口的时间还多。





#### 6.限制子弹数量

射击游戏可能会有同时出现在屏幕上的子弹数量进行限制，以鼓励玩家有目标的射击。

首先，在 settings.py 中存储所允许的最大子弹数：

**settings.py**

```python
	#子弹设置
    self.bullet_speed_factor = 1
    self.bullet_width = 3
    self.bullet_height = 15
    self.bullet_color = 60, 60, 60
    self.bullets_allowed = 3

```

将未消失的子弹数限制为3颗。在 game_functions.py 的 check_keydown_events() 中，在创建新子弹前检查未消失的子弹数是否小于该设置：



**game_functions.py**

```python
def check_keydown_events(event, ai_settings, screen, ship, bullets):
    --snip--
    elif event.key == pygame.K_SPACE:
        #创建新子弹并将其加入到编组 bullets 中
        if len(bullets) < ai_settings.bullets_allowed:
            new_bullet = Bullet(ai_settings, screen, ship)
            bullets.add(new_bullet)

```

玩家按空格键时，检查 bullets 的长度。若 len(bullets) 小于3，创建一个新子弹；但如果已有3颗未消失的子弹，则玩家按空格键时什么都不会发生。若现在运行游戏，屏幕上最多只能有3颗子弹。





#### 7.创建函数 update_bullets()

编写并检查子弹管理代码后，可将其移到模块 game_functions 中，以让主程序文件 alien_invasion.py 尽可能简单。创建一个名为 update_bullets() 的新函数，并将其添加到 game_functions.py 的末尾：

**game_functions.py**

```python
def update_bullets(bullets):
    """更新子弹的位置，并删除已消失的子弹"""
    #更新子弹的位置
    bullets.update()
    
    #删除已消失的子弹
    for bullet in bullets.copy():
        if bullet.rect.bottom <= 0:
            bullets.remove(bullet)

```

* update_bullets() 的代码是从 alien_invasion.py 剪切并粘贴而来的，只需要一个参数，即编组 bullets。



alien_invasion.py 中的 while 循环又比变得很简单了：

**alien_invasion.py**

```python
		#开始游戏主循环
    	whlie True:
            gf.check_events(ai_settings, screen, ship, bullets)
            ship.update()
            gf.update_bullets(bullets)
            gf.update_screen(ai_settings, screen, ship, bullets)

```

主循环中包含尽可能少的代码，如此只看函数名就能迅速知道游戏中发生的情况。主循环检查玩家的输入（第3行代码），然后更新飞船的位置（第4行代码）和所有未消失的子弹的位置（第5行代码）。接下来，使用更新后的位置来绘制新屏幕（第6行代码）。





#### 8.创建函数 fire_bullet()

将发射子弹的代码移到一个独立的函数中，这样，在 check_keydown_events() 中只需使用一行代码来发射子弹，让 elif 代码块变得非常简单：

**game_functions.py**

```python
def check_keydown_events(event, ai_settings, screen, ship, bullets):
    """响应按键"""
    --snip--
    elif event.key == pygame.K_SPACE:
        fire_bullet(ai_settings, screen, ship, bullets)
        
def fire_bullet(ai_settings, screen, ship, bullets):
    """如果还没有达到限制，就发射一颗子弹"""
    #创建新子弹，并将其加入到编组 bullets 中
    if len(bullets) < ai_settings.bullets_allowed:
        new_bullet = Bullet(ai_settings, screen, ship)
        bullets.add(new_bullet)

```

函数 fire_bullet() 只包含玩家按空格键时用于发射子弹的代码；在 check_keydown_events() 中，在玩家按空格键时调用 fire_bullet()。

再次运行 alien_invasion.py，确认发射子弹时依旧没有错误。







## 回顾项目

开发较大项目时，进入每个开发阶段前回顾一下开发计划，知道接下来需要编写哪些任务。本章涉及任务：

* 研究既有代码，确定实现新功能前是否要进行重构。
* 在屏幕左上角添加一个外星人，并指定合适的边距。
* 根据第一个外星人的边距和屏幕尺寸计算屏幕上可容纳多少个外星人。之后将编写一个循环来创建一系列外星人，这些外星人填满了屏幕的上半部分。

* 让外星人群向两边和下方移动，直到外星人被全部击落，有外星人撞到飞船，或有外星人抵达屏幕底端。若整群外星人都被击落，将再创建一群外星人。若有外星人撞到了飞船或抵达屏幕底端，将销毁飞船并再创建一群外星人。
* 限制玩家可用的飞船数量，配给的飞船用完后，游戏结束。

在给项目添加新功能前，应审核既有代码。每进入一个新阶段，通常项目更复杂，因此最好对混乱或低效的代码进行清理。

在开发的同时一直不断重构，因此当前需要清理工作不多，但每次为测试新功能而运行游戏，都必须使用鼠标关闭，下面添加一个结束游戏的快捷键Q：

**game_functions.py**

```python
def check_keydown_events(event, ai_settings, screen, ship, bullets):
    --snip--
    elif event.key == pygame.K_q:
        sys.exit()

```

在 check_keydown_events()中，添加了一个代码块，便于在玩家按 ESC 时结束游戏。

 





## 外星入侵（创建单个、创建多个、目标移动、射杀目标、结束游戏）

### 一、创建单个

在屏幕上放置外星人与放置飞船类似。每个外星人行为都由 Alien 类控制，为简化，使用位图表示外星人。

#### 1.创建 Alien 类

**alien.py**

```python
import pygame
form pygame.sprite import Sprite

class Alien(Sprite):
    """表示单个外星人的类"""
    
    def _init_(self, ai_settings, screen):
        """初始化外星人并设置其起始位置"""
        super(Alien, self)._init_()
        self.screen = screen
        self.ai_settings = ai_settings
        
        #加载外星人图像，并设置其 rect 属性
        self.image = pygame.image.load('image/alien.bmp')
        self.rect = self.image.get_rect()
        
        #每个外星人最初都在屏幕左上角附近
        self.rect.x = self.rect.width
        self.rect.y = self.rect.height
        
        #存储外星人的准确位置
        self.x = float(self.rect.x)
        
	def blitme(self):
        """在指定位置绘制外星人"""
        self.screen.blit(self.image, self.rect)

```

除位置不同外，此类大部分代码都与 Ship 类相似。每个外星人最初都位于屏幕左上角附近，将每个外星人的左边距都设置为外星人的宽度，并将上边距设置为外星人的高度。





#### 2.创建 Alien 实例

在 alien_invasion.py 中创建一个 Alien 实例：

**alien_invasion.py**

```python
--snip--
from ship import Ship
from alien import Alien
import game_functions as gf

def run_game():
    --snip--
    #创建一个外星人
    alien = Alien(ai_settings, screen)
    
    #开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(bullets)
        gf.update_screen(ai_settings, screen, ship, alien, bullets)
        
run_game()

```

导入了新创建的 Alien 类，并在进入主 while 循环前创建了一个 Alien 实例。未修改外星人位置，因此该 while 循环中没有任何新东西，但修改了对 update_screen() 的调用，传递了一个外星人实例。





#### 3.让外星人出现在屏幕上

为让外星人出现在屏幕上，在 update_screen() 中调用其方法 blitme()：

**game_functions.py**

```python
def update_screen(ai_settings, screen, ship, alien, bullets):
    --snip--
    
    #在飞船和外星人后面重绘所有的子弹
    for bullet in bullets:
        bullet.draw_bullet()
    ship.blitme()
    alien.blitme()
    
    #让最近绘制的屏幕可见
    pygame.display.flip()

```

先绘制飞船和子弹，再绘制外星人，让外星人在屏幕上位于最前面。

![1679410295456](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1679410295456.png)







### 二、创建多个

绘制一群外星人，需要确定一行能容纳多少个外星人以及要绘制多少行外星人。首先计算外星人直接的水平间距，并创建一行外星人，再确定可用的垂直空间，并创建整群外星人。

#### 1.确定一行可容纳多少个外星人

为确定一行可容纳多少个外星人，判断可用的水平空间有多大。屏幕宽度存储在 ai_settings，screen_width 中，需要在屏幕两边都留下一定的边距，设置为外星人的宽度。由于有两个边距，因此可用与放置外星人的水平空间为屏幕宽度减去外星人宽度的两倍：

```python
available_space_x = ai_settings.screen_width - (2 * alien_width)

```



还需在外星人之间留出一定的空间，即外星人宽度。因此，显示一个外星人所需的水平空间为外星人宽度的两倍：一个宽度用于放置外星人，另一个宽度为外星人右边的空白区域。为确定一行可容纳多少个外星人，将可用空间除以外星人宽度的两倍：

```python
number_alien_x = available_space_x / (2 * alien_width)

```

将在创建外星人群时使用这些公式。

> **注意**：在程序中执行计算时，一开始无需确定公式是正确的，而可以尝试直接运行程序，测试结果是否符合预期。即便是在最糟糕的情况下，也只是屏幕上显示的外星人太多或太少。可以根据在屏幕上看到的情况调整计算公式。





#### 2.创建多行外星人

为创建一行外星人，首先在 alien_invasion.py 中创建一个名为 aliens 的空编组，用于存储全部外星人，再调用 game_functions.py 中创建外星人群的函数：

**alien_invasion.py**

```python
import pygame
from pygame.sprite import Group

from settings import Settings
from ship import Ship
import game_functions as gf

def run_game():
    --snip--
    #创建一艘飞船、一个子弹编组和一个外星人编组
    ship = Ship(ai_settings, screen)
    bullets = Group()
    aliens = Group()
    
    #创建外星人群
    gf.create_fleet(ai_settings, screen, aliens)
    
    #开始游戏主循环
    while True:
        --snip--
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)
        
run_game()

```

* 由于不再在 alien_invasion.py 中直接创建外星人，因此无需在这个文件中导入 Alien 类。



* 第12行代码行，创建了一个空编组，用于存储所有的外星人。之后，调用稍后将编写的函数 create_fleet()（第15行代码），并将 ai_settings、对象 screen 和空编组 aliens 传递给它。然后，修改对 update_screen() 调用，使得能够访问外星人编组（第20行代码）。



修改 update_screen()：

**game_functions.py**

```python
def update_screen(ai_settings, screen, ship, aliens, bullets):
    --snip--
    ship.blitme()
    aliens.draw(screen)
    
    #让最近绘制的屏幕可见
    pygame.display.flip()

```

对编组调用 draw() 时，python 自动绘制编组的每个元素，绘制位置由元素的属性 rect 决定。aliens.draw(screen) 在屏幕上绘制编组中的每个外星人。





#### 3.创建外星人群

现在可以创建外星人群了。下面是新函数 create_fleet()，放在 game_functions.py 的末尾。还需要导入 Alien 类，因此务必在文件 game_functions.py 开头添加相应的 import 语句：

**game_functions.py**

```python
--snip--
from bullet import Bullet
from alien import Alien
--snip--

def crete_fleet(ai_settings, screen, aliens):
    """创建外星人群"""
    #创建一个外星人，并计算一行可容纳多少个外星人
    #外星人间距为外星人宽度
    alien = Alien(ai_settings, screen)
    alien_width = alien.rect.width
    available_space_x = ai_settings.screen_width - 2 * alien_width
    number_aliens_x = int(available_space_x / (2 * alien_width))
    
    #创建第一行外星人
    for alien_number in range(number_aliens_x):
        #创建一个外星人并将其加入当前行
        alien = Alien(ai_settings, screen)
        alien.x = alien_width + 2 * alien_width * alien_number
        alien.rect.x = alien.x
        aliens.add(alien)
    
```

* 为放置外星人，需要知道外星人的宽度和高度，因此在执行计算前，先创建一个外星人（第10行）。该外星人不是外星人群的成员，因此没有将它加入编组 aliens 中。



* 第11行代码行，从外星人的 rect 属性中获取外星人宽度，并将这个值存储到 alien_width 中，以免反复访问属性 rect。



* 第12行代码行，计算可用于防止外星人的水平空间，已经其中可以容纳多少个外星人。



* 相比于前面介绍的工作，这里唯一的不同是使用了 int() 来确保计算的得到的外星人数量为整数（第13行代码），因为不希望某个外星人不完整，且函数 range() 也需要一个整数。



* 接下来编写了一个循环，从零数到要创建的外星人数（第16行代码）。在循环主体中，创建了一个新的外星人，并通过设置 x 坐标将其加入当前行（第18行代码）。

  将位置信息向右推一个外星人宽度后，再加上外星人的宽度两倍，得到每个外星人占据的空间，乘上已绘制的外星人，得到下一个外星人位置。



* 最后，将所有新创建的外星人加入编组 aliens 中。这行外星人在屏幕上稍微偏向左边，这实际是有好处的，因为接下来将让外星人群往右移，触及屏幕边缘后稍微往下移，然后往左移，以此类推，这种移动方式会比只往下移更有趣。

![1679411332752](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1679411332752.png)






#### 4.重构 create_fleet()

因为创建外星人的工作还未完成，需要清理一下 create_fleet() 函数。除函数本身外，创建两个新函数 get_number_aliens_x() 和 create_alien()：

**game_functions.py**

```python
def get_number_aliens_x(ai_settings, alien_width):
    """计算每行可容纳多少个外星人"""
    available_space_x = ai_settings.screen_width - 2 * alien_width
    number_aliens_x = int(available_space_x / (2 * alien_width))
    return number_aliens_x

def create_alien(ai_settings, screen, aliens, alien_number):
    """创建一个外星人并将其放在当前行"""
    alien = Alien(ai_settings, screen)
    alien_width = alien.rect.width
    alien.x = alien_width + 2 * alien_width * alien_number
    alien.rect.x = alien.x
    aliens.add(alien)
    
def create_fleet(ai_settings, screen, aliens):
    """创建外星人群"""
    #创建一个外星人，并计算每行可容纳多少个外星人
    alien = Alien(ai_settings, screen)
    number_aliens_x = get_number_aliens_x(ai_settings, alien.rect.width)
    
    #创建第一行外星人
    for alien_number in range(number_aliens_x):
        create_alien(ai_settings, screen, aliens, alien_number)

```

* 函数 get_number_aliens_x() 的代码都来自 create_fleet()，且未作任何修改（第1~5行代码）。函数 create_alien() 同（第7~13行代码）。



* 在 create_fleet() 函数中，调用了新建的两个函数，通过这样的重构，添加新行进行创建整群外星人提供便利。





#### 5.添加行

要创建外星人群，需要计算屏幕可容纳多少行，并对创建一行外星人的循环重复相应的次数。

为计算可容纳行数，计算可用垂直空间：将屏幕高度减去第一行外星人的上边距（外星人高度）、飞船的高度以及最初外星人群与飞船的距离（外星人高度的两倍）：

```python
available_space_y = ai_settings.screen_height - 3 * alien_height - ship_height

```

将在飞船上方留出一定的空白区域，给玩家留出射杀外星人的时间。



每行下方都要留出一定的空白区域，并将其设置为外星人的高度。为计算可容纳的行数，将可用垂直空间除以外星人高度的两倍（同样，若计算不对，能马上发现，继而将间距调整为合理的值）。

```python
number_rows = available_height_y / (2 * alien_height)

```



知道可容纳多少行后，便可重复创建一行外星人的代码：

**game_functions.py**

```python
def get_number_rows(ai_settings, ship_height, alien_height):
    """计算屏幕可容纳多少行外星人"""
    available_space_y = (ai_settings.screen_height - (3 * alien_height) - ship_height)
    number_rows = int(available_space_y / (2 * alien_height))
    return number_rows

def create_aliens(ai_settings, screen, aliens, alien_number, row_number):
    --snip--
    alien.x = alien_width + 2 * alien_width * alien_number
    alien.rect.x = alien.x
    alien.rect.y = alien.rect.height + 2 * alien.rect.height * row_number
    aliens.add(alien)
    
def create_fleet(ai_settings, screen, ship, aliens):
    --snip--
    number_aliens_x = get_number_aliens_x(ai_settings, alien.rect.width)
    number_rows = get_number_rows(ai_settings, ship.rect.height, alien.rect.height)
    
    #创建外星人群
    for row_number in range(number_rows):
        for alien_number in range(number_aliens_x):
            crete_alien(ai_settings, screen, aliens, alien_number, row_number)

```

* 为计算屏幕可容纳多少行外星人，在函数 get_number_rows() 中实现了前面计算 available_space_y 和 number_rows 的公式（第1行代码），与 get_number_aliens_x() 类似。



* 第4行代码行，使用 int() 因为不想创建一个不完整的外星人。



<<<<<<< HEAD
* 为创建多行，使用两个嵌套在一起的循环：一个外循环和一个内循环（第20~22行代码）。其中，内部循环创建一行外星人，而外部循环从零数到要创建的外星人行数，使用 range() 函数[range(0, 9) = range(10)]。重复次数，限制于 number_rows。
=======
* 为创建多行，使用两个嵌套在一起的循环：一个外循环和一个内循环（第20~22行代码）。其中，内部循环创建一行外星人，而外部循环从零数到要创建的外星人行数。重复次数，限制于 number_rows。
>>>>>>> 1cf73360cb9792d6ec8c1ae0477849685655edc1



在 create_fleet() 的定义中，新增了一个用于存储 ship 对象的形参，因此在 alien_invasion.py 中调用 create_fleet() 时，需要传递实参 ship：

```python
#创建外星人群
gf.create_fleet(ai_settings, screen, ship, aliens)

```

<<<<<<< HEAD
![1679474558551](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1679474558551.png)

=======
>>>>>>> 1cf73360cb9792d6ec8c1ae0477849685655edc1






### 三、目标移动

下面让外星人群在屏幕上向右移动，撞到屏幕边缘后下移一定距离，再沿相反方向移动。将不断移动所用的外星人，直到所用外星人都被消灭，有外星人撞到飞船，或有外星人抵达屏幕底端。下面先让外星人向右移动。

#### 1.向右移动外星人

为移动外星人，将使用 alien.py 中的方法 update()，且对外星人群中的每个外星人都调用它。首先，添加一个控制外星人速度的设置：

**settings.py**

```python
def __init__(self):
    --snip--
    #外星人设置
    self.alien_speed_factor = 1

```



然后，使用这个设置实现 update()：

**alien.py**

```python
	def update(self):
		"""向右移动外星人"""
        self.x += self.ai_settings.alien_speed_factor
        self.rect.x = self.x
        
```

每次更新外星人位置时，都将它向右移动，移动量为 alien_speed_factor 的值。使用属性 self.x 跟踪每个外星人的准确位置，该属性可存储小数值（第3行代码）。然后使用 self.x 的值更新外星人的 rect 的位置。

在主 while 循环中已调用了更新飞船和子弹的方法，但现在还需要更新每个外星人的位置：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(bullets)
        gf.update_aliens(aliens)
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

更新子弹后再更新外星人的位置，因为稍后要检查是否有子弹撞到外星人。



最后，在文件 game_functions.py 末尾添加函数 update_aliens()：

**game_functions.py**

```python
def update_aliens(aliens):
    """更新外星人群中所有外星人的位置"""
    aliens.update()

```

对编组 aliens 调用方法 update()，将自动对每个外星人调用方法 update()。若现在运行游戏，会看到外星人群向右移，并逐渐消失在屏幕右边缘。





#### 2.创建表示外星人移动方向的设置

下面创建让外星人撞到右屏幕边缘后向下移动、再向左移动的设置。实现这种行为的代码如下：

**settings.py**

```python
	#外星人设置
    self.alien_speed_factor = 1
    self.fleet_drop_speed = 10
    #fleet_direction 为 1 表示向右移，为 -1 表示向左移
    self.fleet_direction = 1

```

设置 fleet_drop_speed 指定了有外星人撞到屏幕边缘时，外星人群向下移动的速度。将这个速度与水平速度分开是有好处的，便于分别调整这两个速度。

要实现 fleet_direction 设置，可以将其设置为文本值，如'left'或’right‘，但这样就必须编写 if-elif 语句来检查外星人群的移动方向。鉴于只有两个可能的方向，使用值 1 和 -1 表示它们，并在外星人群改变方向时在这两个值之间切换。另外鉴于向右移动时需要增大每个外星人的 x 坐标，而向左移动时需要减小每个外星人的 x 坐标，使用数字来表示方向更合理。





#### 3.检查外星人是否撞到了屏幕边缘

现在需要编写一个方法检查是否有外星人撞到了屏幕边缘，还需修改 update()，以让每个外星人都沿着正确的方向移动。

**alien.py**

```python
	def check_edges(self):
    	"""如果外星人位于屏幕边缘，就返回 True"""
    	screen_rect = self.screen.get_rect()
    	if self.rect.right >= screen_rect.right:
        	return True
    	elif self.rect.left <= 0:
            return True
        
    def update(self):
        """向左或向右移动外星人"""
        self.x += (self.ai_settings.alien_speed_factor * self.ai_settings.fleet_direction)
        self.rect.x = self.x

```

* 可对任何外星人调用新方法 check_edges()，判断是否位于左边缘或右边缘。如果外星人的 rect 的 right 属性大于或等于屏幕的 rect 的 right 属性，就说明外星人位于屏幕右边缘（第3行代码）。如果外星人的 rect 的 left 属性小于或等于 0，就说明外星人位于屏幕左边缘（第6行代码）。



* 修改了方法 update()，将移动量设置为外星人速度和 fleet_direction 的乘积，让外星人向左或向右移。

  如果 fleet_direction 为 1，就将外星人当前的 x 坐标增大 alien_speed_factor，从而将外星人向右移；如果 fleet_direction 为 -1，就将外星人当前的 x 坐标减去 alien_speed_factor，从而将外星人向左移。





#### 4.向下移动外星人群并改变移动方向

有外星人到达屏幕边缘时，需要将整群外星人下移，并改变它们的移动方向。需要对 game_fucnctions.py 做重大修改，因为要在这里检查外星人是否到达左边缘或右边缘。

为此，编写函数 check_fleet_edges() 和 change_fleet_direction()，并对 update_aliens() 进行修改：

**game_functions.py**

```python
def check_fleet_edges(ai_settings, aliens):
    """有外星人到达边缘时采取相应的措施"""
    for alien in aliens.sprites():
        if alien.check_edges():
            change_fleet_direction(ai_settings, aliens)
            break
            
def change_fleet_direction(ai_settings, aliens):
    """将整群外星人下移，并改变它们的方向"""
    for alien in aliens.sprites():
        alien.rect.y += ai_settings.fleet_drop_speed
    ai_settings.fleet_direction = -1
    
def update_aliens(ai_settings, aliens):
    """检查是否有外星人位于屏幕边缘，并更新整群外星人的位置"""
    check_fleet_edges(ai_settings, aliens)
    aliens.update()

```

* 在 check_fleet_edges() 中，遍历外星人群，并对其中的每个外星人调用 check_edges()（第3行代码）。

  如果 check_edges() 返回 True，就知道相应的外星人位于屏幕边缘，需要改变外星人群的方向，因此调用 change_fleet_direction() 并退出循环。



* 在 change_fleet_direction() 中，遍历所有外星人，将每个外星人下移 fleet_drop_speed 设置的值（第11行代码）；然后将 fleet_direction() 的值修改为其当前值与 -1 的乘积。



* 修改了函数 update_aliens()，在其中通过调用 check_fleet_edges() 来确定是否有外星人位于屏幕边缘。



现在，函数 update_aliens() 包含形参 ai_settings，因此调用它时制定了与 ai_settings 对应的实参：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(bullets)
        gf.update_aliens(ai_settings, aliens)
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

若现在运行游戏，外星人群将在屏幕上来回移动，并在抵达屏幕边缘后向下移动。现在可用开始射杀外星人，检查是否有外星人撞到飞船，或抵达了屏幕底端。







### 四、射杀目标

创建了飞船和外星人群，但子弹集中外星人时，将穿过外星人，因为还没有检查碰撞。在游戏编程中，碰撞指的是游戏元素重叠在一起。要让子弹能够击落外星人，将使用 sprite.groupcollide() 检测两个编组的成员之间的碰撞。

#### 1.检测子弹与外星人的碰撞

子弹击中外星人时，要马上知道，以便碰撞发生后让外星人立即消失。为此，将在更新子弹的位置后立即检测碰撞。

方法 sprite.groupcollide() 将每颗子弹的 rect 同每个外星人的 rect 进行比较，并返回一个字典，其中包含发生了碰撞得子弹和外星人。在这个字典中，每个键都是一颗子弹，而相应得值都是被击中得外星人（第 14 章实现记分系统时，也会用到这个字典）。

在函数 update_bullets() 中，使用下面得代码来检查碰撞：

**game_functions.py**

```python
def update_bullets(aliens, bullets):
    """更新子弹的位置，并删除已消失的子弹"""
    --snip--
    #检查是否有子弹击中了外星人
    #如果是这样，就删除相应的子弹和外星人
    collisions = pygame.sprite.groupcollide(bullets, aliens, True, True)

```

* 新增的这行代码遍历编组 bullets 中的每颗子弹，再遍历编组 aliens 中的每个外星人。

每当有子弹和外星人的 rect 重叠时， groupcollide() 就在它返回的字典中添加一个键-值对。两个实参 True 告诉 pygame 删除发生碰撞的子弹和外星人。（要模拟能够穿行到屏幕顶端的高能子弹——消灭它击中的每个外星人，可将第一个布尔实参设置为 False，并让第二个布尔实参为 True。这样被击中的外星人将消失，但所有的子弹都始终有效，知道抵达屏幕顶端后消失。）

调用 update_bullets() 时，传递了实参 aliens:

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(aliens, bullets)
        gf.update_aliens(ai_settings, aliens)
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

此时运行游戏，被击中的外星人将消失。





#### 2.为测试创建大子弹

只需通过运行这个游戏就可以测试其很多功能，但有些功能在正常情况下测试起来比较繁琐。例如，要测试代码能否正确的处理外星人编组为空的情形，需要花很长时间将屏幕上的外星人都击落。

测试有些功能时，可以修改游戏的某些设置，以便专注于游戏的特定方面。例如，可以缩小屏幕以减少需要击落的外星人数量，也可以提高子弹的速度，以便能够在单位时间内发射大量子弹。





#### 3.生成新的外星人群

这个游戏的一个重要特点是外星人无穷无尽，一个外星人群被消灭后，又会出现一群外星人。

要在外星人群被消灭后又显示一群外星人，首先需要检查编组 aliens 是否为空。如果为空，就调用 create_fleet()。将在 update_bullets() 中执行这种检查，因为外星人都是在这里被消灭的：

**game_functions.py**

```python
def update_bullets(ai_settings, screen, ship, aliens, bullets):
    --snip--
    #检查是否有子弹击中了外星人
    #如果是，就删除相应的子弹和外星人
    collisions = pygame.sprite.groupcollide(bullets, aliens, True, True)
    
    if len(aliens) = 0:
        #删除现有的子弹并新建一群外星人
        bullets.empty()
        create_fleet(ai_settins, screen, ship, aliens)

```

* 第7~11行代码行，检查编组 aliens 是否为空。如果是，就使用方法 empty() 删除编组中余下的所有精灵，从而删除现有的所有子弹。还调用了 create_fleet()，再次在屏幕上显示一群外星人。



现在，update_bullets() 的定义包含额外的形参 ai_settings、screen 和 ship，因此需要更新 alien_invasion.py 中对 update_bullets() 的调用：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(ai_settings, screen, ship, aliens, bullets)
        gf.update_aliens(ai_settings, aliens)
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

现在，当外星人群消灭干净后，将立刻出现一个新的外星人群。





#### 4.提高子弹的速度

如果现在尝试在这个游戏中射杀外星人，肯发现子弹的速度比以前慢，这是因为在每次循环中，pygame 需要做的工作更多了。为提高子弹的速度，可调整 settings.py 中 bullet_speed_factor 的值。例如，如果将这个值增大到 3 ，子弹在屏幕上向上穿行的速度将变得相当快：

**settings.py**

```python
	#子弹设置
    self.bullet_speed_factor = 3
    self.bullet_width = 3
    --snip--

```

这项设置的最佳值取决于系统速度，请找出适合值。





#### 5.重构 update_bullets()

下面来重构 update_bullets()，使其不再完成那么多任务。将把处理子弹和外星人碰撞的代码移到一个独立的函数中：

**game_functions.py**

```python
def update_bullets(ai_settings, screen, ship, aliens, bullets):
    --snip--
    #删除已消失的子弹
    for bullet in bullets.copy():
        if bullet.rect.bottom <= 0:
            bullets.remove(bullet)
    
    check_bullet_alien_collisions(ai_settings, screen, ship, aliens, bullets)
    
def check_bullet_alien_collisions(ai_settings, screen, ship, aliens, bullets):
    """响应子弹和外星人的碰撞"""
    #删除发生碰撞的子弹和外星人
    collisions = pygame.sprite.groupcollide(bullets, True, True)
    
    if len(aliens) == 0:
        #删除现有的所有子弹，并创建一个新的外星人群
        bullets.empty()
        create_fleet(ai_settings, screen, ship, aliens)
    
```

创建了一个新函数—— check_bullet_alien_collisions()，以检测子弹和外星人之间的碰撞，以及在整群外星人都被消灭干净时采取相应的措施。这避免了 update_bullets() 太长，简化了后续的开发工作。







### 五、结束游戏

如果玩家没能在足够短的时间内将整群外星人都消灭干净，且有外星人撞到了飞船，飞船将被摧毁。与此同时，还限制了可供玩家使用的飞船数，而有外星人抵达屏幕底端时，飞船也将被摧毁。玩家用光了飞船后，游戏便结束。

#### 1.检测外星人和飞船碰撞

首先检查外星人和飞船之间的碰撞，以便外星人撞上飞船时能够做出合作的响应。在更新每个外星人的位置后立即检测外星人和飞船之间的碰撞。

**game_functions.py**

```python
def update_aliens(ai_settings, ship, aliens):
    """
    检查是否有外星人到达屏幕边缘
      然后更新所有外星人的位置
    """
    check_fleet_edges(ai_settings, aliens)
    aliens.update()
    
    #检测外星人和飞船之间的碰撞
    if pygame.sprite.spritecollodeany(ship, aliens):
        print("Ship hit!!!")

```

* 方法 spritecollideany() 接受两个实参：一个精灵和一个编组。它检查编组是否有成员与精灵发生了碰撞，并在找到与精灵发生了碰撞的成员后就停止遍历编组。在这里，它遍历编组 aliens，并返回它找到的第一个与飞船发生了碰撞的外星人。



* 如果没有发生碰撞，spritecollideany() 将返回 None，因此 if 代码块不会执行（第10行代码）。如果找到了与飞船发生碰撞的外星人，它就返回这个外星人，因此 if 代码块将执行：打印”Ship hit!!!“（第11行代码）。

  有外星人撞到飞船时，需要执行的任务很多：需要删除余下的所有外星人和子弹，让飞船重新居中，以及创建一群新的外星人。编写完成这些任务的代码前，需要确定检测外星人和飞船碰撞的方法是否可行。而为确定这一点，最简单的方式时编写一条 print 语句。



现在，需要将 ship 传递给 update_aliens()：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(ai_settings, screen, ship, aliens, bullets)
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

现在如果你运行这个游戏，则每当有外星人撞到飞船时，终端窗口都将显示”Ship hit!!!“。测试这项功能时，请将 alien_drop_speed 设置为较大的值，如 50 或 100，这样外星人将更快的撞到飞船。





#### 2.响应外星人和飞船碰撞

现在需要确定外星人与飞船发生碰撞时，该做些什么。不销毁 ship 实例并创建一个新的 ship 实例，而是通过跟踪游戏的统计信息来记录飞船被撞了多少次（跟踪统计信息还有助于记分）。

下面来编写一个用于跟踪游戏统计信息的新类——GameStats，并将其保存为文件 game_stats.py：

**game_stats.py**

```python
class GameStats():
    """跟踪游戏的统计信息"""
    
    def __init__(self, ai_settings):
        """初始化统计信息"""
        self.ai_settings = ai_settings
        self.reset_stats()
        
    def reset_stats(self):
        """初始化在游戏运行期间可能变化的统计信息"""
        self.ships_left = self.ai_settings.ship_limit

```

* 在这个游戏运行期间，只创建一个 GameStats 实例，但每当玩家开始新游戏时，需要重置一些统计信息。



* 为此，在方法 reset_stats() 中初始化大部分统计信息，而不是在 __ init __ () 中直接初始化它们。在 __ init __() 调用这个方法，这样创建 GameStats 实例时将妥善设置这些统计信息（第7行代码），同时在玩家开始新游戏时也能调用 reset_stats()。



当时只有一项统计信息——ships_left，其值在游戏运行期间将不断变化。一开始玩家拥有的飞船数存储在 settings.py 的 ship_limit 中：

**settings.py**

```python
	#飞船设置
    self.ship_speed_factor = 1.5
    self.ship_limit = 3

```

还需对 alien_invasion.py 做些修改，以创建一个 GameStats 实例：

**alien_invasion.py**

```python
--snip--
from settings import Settings
from game_stats import GameStats
--snip--

def run_game():
    --snip--
    pygame.display.set_caption("Alien Invasion")
    
    #创建一个用于存储游戏统计信息的实例
    stats = GameStats(ai_settings)
    --snip--
    #开始游戏主循环
    while True:
        --snip--
        gf.update_bullets(ai_settings, screen, ship, aliens, bullets)
        gf.update_aliens(ai_settings, stats, screen, ship, aliens, bullets)
        --snip--

```

* 导入了新类 GameStats（第3行代码），创建了一个名为 stats 的实例（第10行代码），再调用 update_aliens() 并添加了实参 stats、screen 和 ship（第17行代码）。



* 在有外星人撞到飞船时，将使用这些实参来跟踪玩家还有多少艘飞船，以及创建一群新的外星人。



有外星人撞到飞船时，将余下的飞船数减 1，创建一群新的外星人，并将飞船重新放置到屏幕底端中央（还将让游戏暂停一段时间，让玩家在新外星人群出现前注意到发生了碰撞，并将重新创建外星人群）。

下面将实现这些功能的大部分代码放到函数 ship_hit() 中：

**game_functions.py**

```python
import sys
from time import sleep

import pygame
--snip--

def ship_hit(ai_settings, stats, screen, ship, aliens, bullets):
    """响应被外星人撞到的飞船"""
    #将 ships_left 减 1
    stats.ships_left -= 1
    
    #清空外星人列表和子弹列表
    aliens.empty()
    bullets.empty()
    
    #创建一群新的外星人，并将飞船放到屏幕底端中央
    create_fleet(ai_settings, screen, ship, aliens)
    ship.center_ship()
    
    #暂停
    sleep(0.5)
    
def update_aliens(ai_settings, stats, screen, ship, aliens, bullets):
    --snip--
    #检测外星人和飞船碰撞
    if pygame.sprite.spritecollideany(ship, aliens):
        ship_hit(ai_settings, stats, screen, ship, aliens, bullets)

```

* 首先从模块 time 中导入了函数 sleep()，以便使用它来让游戏暂停（第2行代码）。



* 新函数 ship_hit() 在飞船被外星人撞到时作出响应。在这个函数内部，将余下的飞船数减 1（第10行代码），然后清空编组 aliens 和 bullets（第13、14行代码）。



* 接下来，创建一群新的外星人，并将飞船居中（第17、18行代码），稍后将在 Ship 类中添加方法 center_ship()。



* 最后，更新所有元素后（但在将修改显示到屏幕前）暂停，让玩家知道其飞船被撞到了（第21行代码）。

  屏幕将暂时停止变化，让玩家能够看到外星人撞到了飞船。函数 sleep() 执行完毕后，将接着执行函数 update_screen()，将新的外星人群绘制到屏幕上。



* 还更新了 update_aliens() 的定义，使其包含形参 stats、screen 和 bullets(第23行代码)，让它能够在调用 ship_hit() 时传递这些值。



下面是新方法 center_ship()，请将其添加到 ship.py 的末尾：

**ship.py**

```python
def center_ship(self):
    """让飞船在屏幕上居中"""
    self.center = self.screen_rect.centerx

```

为让飞船居中，将飞船的属性 center 设置为屏幕中心的 x 坐标，而该坐标是通过属性 screen_rect 获得的。

> 注意：根本没有创建多艘飞船，在整个游戏运行期间，都只创建了一个飞船实例，并在该飞船被撞到时将其居中。统计信息 ships_left 使得知道飞船是否用完。

运行这个游戏，射杀几个外星人，并让一个外星人撞到飞船。游戏暂停后，将出现一群新的外星人，而飞船将在屏幕底端居中。





#### 3.有外星人到达屏幕底端

如果有外星人到达屏幕底端，将像有外星人撞到飞船那样做出响应。请添加一个执行这些任务的新函数，并将其命名为 update_aliens()：

**game_functions.py**

```python
def check_aliens_bottom(ai_settings, stats, screen, ship, aliens, bullets):
    """检查是否有外星人到达了屏幕底端"""
    screen_rect = screen.get_rect()
    for alien in aliens.sprites():
        if alien.rect.bottom >= screen_rect.bottom:
            #像飞船被撞到一样进行处理
            ship_hit(ai_settings, stats, screen, ship, aliens, bullets)
            break
            
def update_aliens(ai_settings, stats, screen, ship, aliens, bullets):
    --snip--
    #检查是否有外星人到达屏幕底端
    check_aliens_bottom(ai_settings, stats, screen, ship, aliens, bullets)

```

* 函数 check_aliens_bottom() 检查是否有外星人到达了屏幕底端。到达屏幕底端后，外星人的属性 rect.bottom 的值大于或等于屏幕的属性 rect.bottom 的值（第5行代码）。

  如果有外星人到达屏幕底端，就调用 ship_hit()；只要检测到一个外星人到达屏幕底端，就无需检查其他外星人，因此在调用 ship_hit() 后退出循环。



* 在更新所有外星人的位置并检测是否有外星人和飞船发生碰撞后调用 check_aliens_bottom()（第13行代码）。

  现在，每当有外星人撞到飞船或抵达屏幕底端时，都将出现一群新的外星人。





#### 4.游戏结束

现在这个游戏看起来更完整了，但它永远都不会结束，只是 ships_left 不断变成更小的负数。下面在 GameStats 中添加一个作为标志的属性 game_active，以便在玩家的飞船用完后结束游戏：

**game_stats.py**

```python
	def __init__(self, settings):
        --snip--
        #游戏刚启动时处于活动状态
        self.game_active = True

```

现在在 ship_hit() 中添加代码，在玩家的飞船都用完后将 game_active 设置为 False：

**game_functions.py**

```python
def ship_hit(ai_settings, stats, screen, ship, aliens, bullets):
    """响应飞船被外星人撞到"""
    if stats.ships_left > 0:
        #将 ships_left 减 1
        stats.ships_left -= 1
        --snip--
        #暂停一会儿
        sleep(0.5)
        
    else:
        stats.game_active = False

```

ship_hit() 的大部分代码都没变。将原理的所有代码都移到了一个 if 语句块中，这条 if 语句检查玩家是否至少还有一艘飞船。如果是这样，就创建一群新的外星人，暂停一会儿，再接着往下执行。如果玩家没有飞船了，就将 game_active 设置为 False。





#### 5.确定应运行游戏的哪些部分

在 alien_invasion.py 中，需要确定游戏的哪些部分在任何情况下都应运行，哪些部分仅在游戏处于活动状态时才运行：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        
        if stats.game_active:
            ship.update()
            gf.update_bullets(ai_settings, screen, ship, aliens, bullets)
            gf.update_aliens(ai_settings, stats, screen, ship, aliens, bullets)
            
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

在主循环中，在任何情况下都需要调用 check_events()，即便游戏处于非活动状态时亦如此。

例如，需要知道玩家是否按了 Q 键以退出游戏，或单击关闭窗口的按钮。还需要不断更新屏幕，以便在等待玩家是否选择开始新游戏时能够修改屏幕。其他的函数仅在游戏处于活动状态时才需要调用，因为游戏处于非活动状态时，不用更新游戏元素的位置。

现在，运行游戏时，将在飞船用完后停止不动。







## 小结项目

在以上项目中，学习了：如何在游戏中添加大量相同的元素，如创建一群外星人；如何使用嵌套循环来创建元素网格，还通过调用每个元素的方法 update() 移动了大量的元素；如何控制对象在屏幕上移动的方向，以及如何响应事件；如有外星人达到屏幕边缘，如何检测和响应子弹和外星人碰撞；如何在游戏中跟踪统计信息，以及如何使用标志 game_active 来判断游戏是否结束了。

之后，在这个项目最后一项，将添加一个 Play 按钮，让玩家能够开始游戏，以及游戏结束后再玩。每当玩家消灭一群外星人后，都将加快游戏的节奏，并添加一个记分系统，得到一个极具可玩性的游戏。







## 计算得分（添加按钮、提高等级、统计分数）










>>>>>>> 1cf73360cb9792d6ec8c1ae0477849685655edc1

在主 while 循环中已调用了更新飞船和子弹的方法，但现在还需要更新每个外星人的位置：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(bullets)
        gf.update_aliens(aliens)
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

更新子弹后再更新外星人的位置，因为稍后要检查是否有子弹撞到外星人。



最后，在文件 game_functions.py 末尾添加函数 update_aliens()：

**game_functions.py**

```python
def update_aliens(aliens):
    """更新外星人群中所有外星人的位置"""
    aliens.update()

```

对编组 aliens 调用方法 update()，将自动对每个外星人调用方法 update()。若现在运行游戏，会看到外星人群向右移，并逐渐消失在屏幕右边缘。

![1679475429128](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1679475429128.png)





#### 2.创建表示外星人移动方向的设置

下面创建让外星人撞到右屏幕边缘后向下移动、再向左移动的设置。实现这种行为的代码如下：

**settings.py**

```python
	#外星人设置
    self.alien_speed_factor = 1
    self.fleet_drop_speed = 10
    #fleet_direction 为 1 表示向右移，为 -1 表示向左移
    self.fleet_direction = 1

```

设置 fleet_drop_speed 指定了有外星人撞到屏幕边缘时，外星人群向下移动的速度。将这个速度与水平速度分开是有好处的，便于分别调整这两个速度。

要实现 fleet_direction 设置，可以将其设置为文本值，如'left'或’right‘，但这样就必须编写 if-elif 语句来检查外星人群的移动方向。鉴于只有两个可能的方向，使用值 1 和 -1 表示它们，并在外星人群改变方向时在这两个值之间切换。另外鉴于向右移动时需要增大每个外星人的 x 坐标，而向左移动时需要减小每个外星人的 x 坐标，使用数字来表示方向更合理。





#### 3.检查外星人是否撞到了屏幕边缘

现在需要编写一个方法检查是否有外星人撞到了屏幕边缘，还需修改 update()，以让每个外星人都沿着正确的方向移动。

**alien.py**

```python
	def check_edges(self):
    	"""如果外星人位于屏幕边缘，就返回 True"""
    	screen_rect = self.screen.get_rect()
    	if self.rect.right >= screen_rect.right:
        	return True
    	elif self.rect.left <= 0:
            return True
        
    def update(self):
        """向左或向右移动外星人"""
        self.x += (self.ai_settings.alien_speed_factor * self.ai_settings.fleet_direction)
        self.rect.x = self.x

```

* 可对任何外星人调用新方法 check_edges()，判断是否位于左边缘或右边缘。如果外星人的 rect 的 right 属性大于或等于屏幕的 rect 的 right 属性，就说明外星人位于屏幕右边缘（第3行代码）。如果外星人的 rect 的 left 属性小于或等于 0，就说明外星人位于屏幕左边缘（第6行代码）。



* 修改了方法 update()，将移动量设置为外星人速度和 fleet_direction 的乘积，让外星人向左或向右移。

  如果 fleet_direction 为 1，就将外星人当前的 x 坐标增大 alien_speed_factor，从而将外星人向右移；如果 fleet_direction 为 -1，就将外星人当前的 x 坐标减去 alien_speed_factor，从而将外星人向左移。





#### 4.向下移动外星人群并改变移动方向

有外星人到达屏幕边缘时，需要将整群外星人下移，并改变它们的移动方向。需要对 game_fucnctions.py 做重大修改，因为要在这里检查外星人是否到达左边缘或右边缘。

为此，编写函数 check_fleet_edges() 和 change_fleet_direction()，并对 update_aliens() 进行修改：

**game_functions.py**

```python
def check_fleet_edges(ai_settings, aliens):
    """有外星人到达边缘时采取相应的措施"""
    for alien in aliens.sprites():
        if alien.check_edges():
            change_fleet_direction(ai_settings, aliens)
            break
            
def change_fleet_direction(ai_settings, aliens):
    """将整群外星人下移，并改变它们的方向"""
    for alien in aliens.sprites():
        alien.rect.y += ai_settings.fleet_drop_speed
    ai_settings.fleet_direction *= -1
    
def update_aliens(ai_settings, aliens):
    """检查是否有外星人位于屏幕边缘，并更新整群外星人的位置"""
    check_fleet_edges(ai_settings, aliens)
    aliens.update()

```

* 在 check_fleet_edges() 中，遍历外星人群，并对其中的每个外星人调用 check_edges()（第3行代码）。

  如果 check_edges() 返回 True，就知道相应的外星人位于屏幕边缘，需要改变外星人群的方向，因此调用 change_fleet_direction() 并退出循环。



* 在 change_fleet_direction() 中，遍历所有外星人，将每个外星人下移 fleet_drop_speed 设置的值（第11行代码）；然后将 fleet_direction() 的值修改为其当前值与 -1 的乘积。



* 修改了函数 update_aliens()，在其中通过调用 check_fleet_edges() 来确定是否有外星人位于屏幕边缘。



现在，函数 update_aliens() 包含形参 ai_settings，因此调用它时制定了与 ai_settings 对应的实参：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(bullets)
        gf.update_aliens(ai_settings, aliens)
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

若现在运行游戏，外星人群将在屏幕上来回移动，并在抵达屏幕边缘后向下移动。现在可用开始射杀外星人，检查是否有外星人撞到飞船，或抵达了屏幕底端。

![1679484080208](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1679484080208.png)







### 四、射杀目标

创建了飞船和外星人群，但子弹集中外星人时，将穿过外星人，因为还没有检查碰撞。在游戏编程中，碰撞指的是游戏元素重叠在一起。要让子弹能够击落外星人，将使用 sprite.groupcollide() 检测两个编组的成员之间的碰撞。

#### 1.检测子弹与外星人的碰撞

子弹击中外星人时，要马上知道，以便碰撞发生后让外星人立即消失。为此，将在更新子弹的位置后立即检测碰撞。

方法 sprite.groupcollide() 将每颗子弹的 rect 同每个外星人的 rect 进行比较，并返回一个字典，其中包含发生了碰撞得子弹和外星人。在这个字典中，每个键都是一颗子弹，而相应得值都是被击中得外星人（第 14 章实现记分系统时，也会用到这个字典）。

在函数 update_bullets() 中，使用下面得代码来检查碰撞：

**game_functions.py**

```python
def update_bullets(aliens, bullets):
    """更新子弹的位置，并删除已消失的子弹"""
    --snip--
    #检查是否有子弹击中了外星人
    #如果是这样，就删除相应的子弹和外星人
    collisions = pygame.sprite.groupcollide(bullets, aliens, True, True)

```

* 新增的这行代码遍历编组 bullets 中的每颗子弹，再遍历编组 aliens 中的每个外星人。

每当有子弹和外星人的 rect 重叠时， groupcollide() 就在它返回的字典中添加一个键-值对。两个实参 True 告诉 pygame 删除发生碰撞的子弹和外星人。

（要模拟能够穿行到屏幕顶端的高能子弹——消灭它击中的每个外星人，可将第一个布尔实参设置为 False，并让第二个布尔实参为 True。这样被击中的外星人将消失，但所有的子弹都始终有效，知道抵达屏幕顶端后消失。）

调用 update_bullets() 时，传递了实参 aliens:

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(aliens, bullets)
        gf.update_aliens(ai_settings, aliens)
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

此时运行游戏，被击中的外星人将消失。

![1679484354111](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1679484354111.png)





#### 2.为测试创建大子弹

只需通过运行这个游戏就可以测试其很多功能，但有些功能在正常情况下测试起来比较繁琐。例如，要测试代码能否正确的处理外星人编组为空的情形，需要花很长时间将屏幕上的外星人都击落。

测试有些功能时，可以修改游戏的某些设置，以便专注于游戏的特定方面。例如，可以缩小屏幕以减少需要击落的外星人数量，也可以提高子弹的速度，以便能够在单位时间内发射大量子弹。

![1679484799348](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1679484799348.png)





#### 3.生成新的外星人群

这个游戏的一个重要特点是外星人无穷无尽，一个外星人群被消灭后，又会出现一群外星人。

要在外星人群被消灭后又显示一群外星人，首先需要检查编组 aliens 是否为空。如果为空，就调用 create_fleet()。将在 update_bullets() 中执行这种检查，因为外星人都是在这里被消灭的：

**game_functions.py**

```python
def update_bullets(ai_settings, screen, ship, aliens, bullets):
    --snip--
    #检查是否有子弹击中了外星人
    #如果是，就删除相应的子弹和外星人
    collisions = pygame.sprite.groupcollide(bullets, aliens, True, True)
    
    if len(aliens) == 0:
        #删除现有的子弹并新建一群外星人
        bullets.empty()
        create_fleet(ai_settins, screen, ship, aliens)

```

* 第7~11行代码行，检查编组 aliens 是否为空。如果是，就使用方法 empty() 删除编组中余下的所有精灵，从而删除现有的所有子弹。还调用了 create_fleet()，再次在屏幕上显示一群外星人。



现在，update_bullets() 的定义包含额外的形参 ai_settings、screen 和 ship，因此需要更新 alien_invasion.py 中对 update_bullets() 的调用：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(ai_settings, screen, ship, aliens, bullets)
        gf.update_aliens(ai_settings, aliens)
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

现在，当外星人群消灭干净后，将立刻出现一个新的外星人群。





#### 4.提高子弹的速度

如果现在尝试在这个游戏中射杀外星人，肯发现子弹的速度比以前慢，这是因为在每次循环中，pygame 需要做的工作更多了。为提高子弹的速度，可调整 settings.py 中 bullet_speed_factor 的值。例如，如果将这个值增大到 3 ，子弹在屏幕上向上穿行的速度将变得相当快：

**settings.py**

```python
	#子弹设置
    self.bullet_speed_factor = 3
    self.bullet_width = 3
    --snip--

```

这项设置的最佳值取决于系统速度，请找出适合值。





#### 5.重构 update_bullets()

下面来重构 update_bullets()，使其不再完成那么多任务。将把处理子弹和外星人碰撞的代码移到一个独立的函数中：

**game_functions.py**

```python
def update_bullets(ai_settings, screen, ship, aliens, bullets):
    --snip--
    #删除已消失的子弹
    for bullet in bullets.copy():
        if bullet.rect.bottom <= 0:
            bullets.remove(bullet)
    
    check_bullet_alien_collisions(ai_settings, screen, ship, aliens, bullets)
    
def check_bullet_alien_collisions(ai_settings, screen, ship, aliens, bullets):
    """响应子弹和外星人的碰撞"""
    #删除发生碰撞的子弹和外星人
    collisions = pygame.sprite.groupcollide(bullets, True, True)
    
    if len(aliens) == 0:
        #删除现有的所有子弹，并创建一个新的外星人群
        bullets.empty()
        create_fleet(ai_settings, screen, ship, aliens)
    
```

创建了一个新函数—— check_bullet_alien_collisions()，以检测子弹和外星人之间的碰撞，以及在整群外星人都被消灭干净时采取相应的措施。这避免了 update_bullets() 太长，简化了后续的开发工作。







### 五、结束游戏

如果玩家没能在足够短的时间内将整群外星人都消灭干净，且有外星人撞到了飞船，飞船将被摧毁。与此同时，还限制了可供玩家使用的飞船数，而有外星人抵达屏幕底端时，飞船也将被摧毁。玩家用光了飞船后，游戏便结束。

#### 1.检测外星人和飞船碰撞

首先检查外星人和飞船之间的碰撞，以便外星人撞上飞船时能够做出合作的响应。在更新每个外星人的位置后立即检测外星人和飞船之间的碰撞。

**game_functions.py**

```python
def update_aliens(ai_settings, ship, aliens):
    """
    检查是否有外星人到达屏幕边缘
      然后更新所有外星人的位置
    """
    check_fleet_edges(ai_settings, aliens)
    aliens.update()
    
    #检测外星人和飞船之间的碰撞
    if pygame.sprite.spritecollideany(ship, aliens):
        print("Ship hit!!!")

```

* 方法 spritecollideany() 接受两个实参：一个精灵和一个编组。它检查编组是否有成员与精灵发生了碰撞，并在找到与精灵发生了碰撞的成员后就停止遍历编组。在这里，它遍历编组 aliens，并返回它找到的第一个与飞船发生了碰撞的外星人。



* 如果没有发生碰撞，spritecollideany() 将返回 None，因此 if 代码块不会执行（第10行代码）。如果找到了与飞船发生碰撞的外星人，它就返回这个外星人，因此 if 代码块将执行：打印”Ship hit!!!“（第11行代码）。

  有外星人撞到飞船时，需要执行的任务很多：需要删除余下的所有外星人和子弹，让飞船重新居中，以及创建一群新的外星人。编写完成这些任务的代码前，需要确定检测外星人和飞船碰撞的方法是否可行。而为确定这一点，最简单的方式时编写一条 print 语句。



现在，需要将 ship 传递给 update_aliens()：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        ship.update()
        gf.update_bullets(ai_settings, screen, ship, aliens, bullets)
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

现在如果你运行这个游戏，则每当有外星人撞到飞船时，终端窗口都将显示”Ship hit!!!“。测试这项功能时，请将 alien_drop_speed 设置为较大的值，如 50 或 100，这样外星人将更快的撞到飞船。





#### 2.响应外星人和飞船碰撞

现在需要确定外星人与飞船发生碰撞时，该做些什么。不销毁 ship 实例并创建一个新的 ship 实例，而是通过跟踪游戏的统计信息来记录飞船被撞了多少次（跟踪统计信息还有助于记分）。

下面来编写一个用于跟踪游戏统计信息的新类——GameStats，并将其保存为文件 game_stats.py：

**game_stats.py**

```python
class GameStats():
    """跟踪游戏的统计信息"""
    
    def __init__(self, ai_settings):
        """初始化统计信息"""
        self.ai_settings = ai_settings
        self.reset_stats()
        
    def reset_stats(self):
        """初始化在游戏运行期间可能变化的统计信息"""
        self.ships_left = self.ai_settings.ship_limit

```

* 在这个游戏运行期间，只创建一个 GameStats 实例，但每当玩家开始新游戏时，需要重置一些统计信息。



* 为此，在方法 reset_stats() 中初始化大部分统计信息，而不是在 __ init __ () 中直接初始化它们。在 __ init __() 调用这个方法，这样创建 GameStats 实例时将妥善设置这些统计信息（第7行代码），同时在玩家开始新游戏时也能调用 reset_stats()。



当时只有一项统计信息——ships_left，其值在游戏运行期间将不断变化。一开始玩家拥有的飞船数存储在 settings.py 的 ship_limit 中：

**settings.py**

```python
	#飞船设置
    self.ship_speed_factor = 1.5
    self.ship_limit = 3

```

还需对 alien_invasion.py 做些修改，以创建一个 GameStats 实例：

**alien_invasion.py**

```python
--snip--
from settings import Settings
from game_stats import GameStats
--snip--

def run_game():
    --snip--
    pygame.display.set_caption("Alien Invasion")
    
    #创建一个用于存储游戏统计信息的实例
    stats = GameStats(ai_settings)
    --snip--
    #开始游戏主循环
    while True:
        --snip--
        gf.update_bullets(ai_settings, screen, ship, aliens, bullets)
        gf.update_aliens(ai_settings, stats, screen, ship, aliens, bullets)
        --snip--

```

* 导入了新类 GameStats（第3行代码），创建了一个名为 stats 的实例（第10行代码），再调用 update_aliens() 并添加了实参 stats、screen 和 ship（第17行代码）。



* 在有外星人撞到飞船时，将使用这些实参来跟踪玩家还有多少艘飞船，以及创建一群新的外星人。



有外星人撞到飞船时，将余下的飞船数减 1，创建一群新的外星人，并将飞船重新放置到屏幕底端中央（还将让游戏暂停一段时间，让玩家在新外星人群出现前注意到发生了碰撞，并将重新创建外星人群）。

下面将实现这些功能的大部分代码放到函数 ship_hit() 中：

**game_functions.py**

```python
import sys
from time import sleep

import pygame
--snip--

def ship_hit(ai_settings, stats, screen, ship, aliens, bullets):
    """响应被外星人撞到的飞船"""
    #将 ships_left 减 1
    stats.ships_left -= 1
    
    #清空外星人列表和子弹列表
    aliens.empty()
    bullets.empty()
    
    #创建一群新的外星人，并将飞船放到屏幕底端中央
    create_fleet(ai_settings, screen, ship, aliens)
    ship.center_ship()
    
    #暂停
    sleep(0.5)
    
def update_aliens(ai_settings, stats, screen, ship, aliens, bullets):
    --snip--
    #检测外星人和飞船碰撞
    if pygame.sprite.spritecollideany(ship, aliens):
        ship_hit(ai_settings, stats, screen, ship, aliens, bullets)

```

* 首先从模块 time 中导入了函数 sleep()，以便使用它来让游戏暂停（第2行代码）。



* 新函数 ship_hit() 在飞船被外星人撞到时作出响应。在这个函数内部，将余下的飞船数减 1（第10行代码），然后清空编组 aliens 和 bullets（第13、14行代码）。



* 接下来，创建一群新的外星人，并将飞船居中（第17、18行代码），稍后将在 Ship 类中添加方法 center_ship()。



* 最后，更新所有元素后（但在将修改显示到屏幕前）暂停，让玩家知道其飞船被撞到了（第21行代码）。

  屏幕将暂时停止变化，让玩家能够看到外星人撞到了飞船。函数 sleep() 执行完毕后，将接着执行函数 update_screen()，将新的外星人群绘制到屏幕上。



* 还更新了 update_aliens() 的定义，使其包含形参 stats、screen 和 bullets(第23行代码)，让它能够在调用 ship_hit() 时传递这些值。



下面是新方法 center_ship()，请将其添加到 ship.py 的末尾：

**ship.py**

```python
def center_ship(self):
    """让飞船在屏幕上居中"""
    self.center = self.screen_rect.centerx

```

为让飞船居中，将飞船的属性 center 设置为屏幕中心的 x 坐标，而该坐标是通过属性 screen_rect 获得的。

> 注意：根本没有创建多艘飞船，在整个游戏运行期间，都只创建了一个飞船实例，并在该飞船被撞到时将其居中。统计信息 ships_left 使得知道飞船是否用完。

运行这个游戏，射杀几个外星人，并让一个外星人撞到飞船。游戏暂停后，将出现一群新的外星人，而飞船将在屏幕底端居中。





#### 3.有外星人到达屏幕底端

如果有外星人到达屏幕底端，将像有外星人撞到飞船那样做出响应。请添加一个执行这些任务的新函数，并将其命名为 update_aliens()：

**game_functions.py**

```python
def check_aliens_bottom(ai_settings, stats, screen, ship, aliens, bullets):
    """检查是否有外星人到达了屏幕底端"""
    screen_rect = screen.get_rect()
    for alien in aliens.sprites():
        if alien.rect.bottom >= screen_rect.bottom:
            #像飞船被撞到一样进行处理
            ship_hit(ai_settings, stats, screen, ship, aliens, bullets)
            break
            
def update_aliens(ai_settings, stats, screen, ship, aliens, bullets):
    --snip--
    #检查是否有外星人到达屏幕底端
    check_aliens_bottom(ai_settings, stats, screen, ship, aliens, bullets)

```

* 函数 check_aliens_bottom() 检查是否有外星人到达了屏幕底端。到达屏幕底端后，外星人的属性 rect.bottom 的值大于或等于屏幕的属性 rect.bottom 的值（第5行代码）。

  如果有外星人到达屏幕底端，就调用 ship_hit()；只要检测到一个外星人到达屏幕底端，就无需检查其他外星人，因此在调用 ship_hit() 后退出循环。



* 在更新所有外星人的位置并检测是否有外星人和飞船发生碰撞后调用 check_aliens_bottom()（第13行代码）。

  现在，每当有外星人撞到飞船或抵达屏幕底端时，都将出现一群新的外星人。





#### 4.游戏结束

现在这个游戏看起来更完整了，但它永远都不会结束，只是 ships_left 不断变成更小的负数。下面在 GameStats 中添加一个作为标志的属性 game_active，以便在玩家的飞船用完后结束游戏：

**game_stats.py**

```python
	def __init__(self, settings):
        --snip--
        #游戏刚启动时处于活动状态
        self.game_active = True

```

现在在 ship_hit() 中添加代码，在玩家的飞船都用完后将 game_active 设置为 False：

**game_functions.py**

```python
def ship_hit(ai_settings, stats, screen, ship, aliens, bullets):
    """响应飞船被外星人撞到"""
    if stats.ships_left > 0:
        #将 ships_left 减 1
        stats.ships_left -= 1
        --snip--
        #暂停一会儿
        sleep(0.5)
        
    else:
        stats.game_active = False

```

ship_hit() 的大部分代码都没变。将原理的所有代码都移到了一个 if 语句块中，这条 if 语句检查玩家是否至少还有一艘飞船。如果是这样，就创建一群新的外星人，暂停一会儿，再接着往下执行。如果玩家没有飞船了，就将 game_active 设置为 False。





#### 5.确定应运行游戏的哪些部分

在 alien_invasion.py 中，需要确定游戏的哪些部分在任何情况下都应运行，哪些部分仅在游戏处于活动状态时才运行：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, ship, bullets)
        
        if stats.game_active:
            ship.update()
            gf.update_bullets(ai_settings, screen, ship, aliens, bullets)
            gf.update_aliens(ai_settings, stats, screen, ship, aliens, bullets)
            
        gf.update_screen(ai_settings, screen, ship, aliens, bullets)

```

在主循环中，在任何情况下都需要调用 check_events()，即便游戏处于非活动状态时亦如此。

例如，需要知道玩家是否按了 Q 键以退出游戏，或单击关闭窗口的按钮。还需要不断更新屏幕，以便在等待玩家是否选择开始新游戏时能够修改屏幕。其他的函数仅在游戏处于活动状态时才需要调用，因为游戏处于非活动状态时，不用更新游戏元素的位置。

现在，运行游戏时，将在飞船用完后停止不动。

![1679487977275](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1679487977275.png)







## 小结项目

在以上项目中，学习了：如何在游戏中添加大量相同的元素，如创建一群外星人；如何使用嵌套循环来创建元素网格，还通过调用每个元素的方法 update() 移动了大量的元素；如何控制对象在屏幕上移动的方向，以及如何响应事件；如有外星人达到屏幕边缘，如何检测和响应子弹和外星人碰撞；如何在游戏中跟踪统计信息，以及如何使用标志 game_active 来判断游戏是否结束了。

之后，在这个项目最后一项，将添加一个 Play 按钮，让玩家能够开始游戏，以及游戏结束后再玩。每当玩家消灭一群外星人后，都将加快游戏的节奏，并添加一个记分系统，得到一个极具可玩性的游戏。







## 计算得分（添加按钮、提高等级、统计分数）

即将结束游戏《外星入侵》的开发。将添加一个 Play 按钮，用于根据需要启动游戏以及在游戏结束后重启游戏。还将修改这个游戏，使其在玩家的等级提高时加快节奏，并实现一个计分系统。接下来，将掌握足够多的知识，能够开始编写随玩家等级提高而加大难度以及显示得分的游戏。

### 一、添加按钮

在本节中，将添加一个 Play 按钮，在游戏开始前出现，并在游戏结束后再次出现，让玩家能够开始新游戏。

当前，这个游戏在玩家运行 alien_invasion.py 时就开始了。下面让游戏一开始处于非活动状态，并提示玩家单击 Play 按钮来开始游戏。

在 game_stats.py 中输入如下代码：

**game_stats.py**

```python
	def __init__(self, ai_settings):
        """初始化统计信息"""
        self.ai_settings = ai_settings
        self.reset_stats()
        
        #让游戏一开始处于非活动状态
        self.game_active = False
        
    def reset_stats(self):
        --snip--

```

现在游戏一开始将处于非活动状态，等创建 Play 按钮后，玩家才能开始游戏。

#### 1.创建 Button 类

由于 Pygame 没用内置创建按钮的方法，创建一个 Button 类，用于创建带标签的实心矩形。可以在游戏中使用其中的代码，创建任何按钮。

下面编写 Button 类的第一部分，将该类保存为文件 button.py：

**button.py**

```python
import pygame.font

class Button():
    
    def __init__(self, ai_settings, screen, msg):
        """初始化按钮的属性"""
        self.screen = screen
        self.screen_rect = screen.get_rect()
        
        #设置按钮的尺寸和其他属性
        self.width, self.height = 200, 50
        self.button_color = (255, 0, 0)
        self.text_color = (255, 255, 255)
        self.font = pygame.font.SysFont(None, 48)
        
        #创建按钮的 rect 对象，并使其居中
        self.rect = pygame.Rect(0, 0, self.width, self.height)
        self.rect.center = self.screen_rect.center
        
        #按钮的标签只需创建一次
        self.prep_msg(msg)

```

* 首先，导入了 pygame 中的文本模块 pygame.font，它让 Pygame 能够将文本渲染到屏幕上。方法 __ init __() 接受参数 self，对象 ai_settings 和 screen，以及 msg，其中 msg 是要再按钮中显示的文本（第1行代码）。



* 设置按钮的尺寸和颜色，并设置 text_color 让文本颜色为白色（第11~13行代码）。



* 第14行代码行，指定使用什么字体渲染文本。实参 None 让 Pygame 使用默认字体，而 48 制定了文本字体的字号。



* 第17、18行代码行，为让按钮在屏幕上居中，创建了一个表示按钮的 tect 对象，并将其 center 属性设置为屏幕的 center 属性。



* pygame 通过将要显示的字符串渲染为图像来处理文本。第25行代码行，调用 prep_msg() 处理这样的渲染。

rect = pygame.Rect(left, top, width, height) 使用 Rect() 方法创建一个指定位置、大小的矩形区域，Rect 表示的区域必须位于一个 Surface 对象之上，比如游戏的主窗口（screen）。四个关键参数的构成如下：

![1679553711786](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1679553711786.png)



prep_msg() 的代码如下：

**button.py**

```python
	def prep_msg(self, msg):
        """将 msg 渲染为图像，并使其在按钮上居中"""
        self.msg_image = self.font.render(msg, True, self.text_color,
                                         self.button_color)
        self.msg_image_rect = self.msg_image.get_rect()
        self.msg_image_rect.center = self.rect.center

```

* 方法 prep_msg() 接受实参 self 以及要渲染为图像的文本（msg）。

  调用 font.render() 将存储在 msg 中的文本转换为图像，然后将该图像存储在 msg_image 中（第3行代码）。方法 font.render() 还接受一个布尔实参，该实参指定开启还是关闭反锯齿功能（反锯齿让文本的边缘更平滑）。余下的两个实参，分别是文本颜色和背景色。

  启用了反锯齿功能，并将文本的背景色设置为按钮的颜色（如果没有指定背景色，Pygame 将以透明背景的方式渲染文本）。



* 第5、6行代码行，让文本图像在按钮上居中：根据文本图像创建一个 rect，并将其 center 属性设置为按钮的 center 属性。



最后，创建方法 draw_button()，通过调用它可将这个按钮显示到屏幕上：

**button.py**

```python
	def draw_button(self):
        #绘制一个用颜色填充的按钮，再绘制文本
        self.screen.fill(self.button_color, self.rect)
        self.screen.blit(self.msg_image, self.msg_image_rect)

```

调用 screen.fill() 来绘制表示按钮的矩形，再调用 screen.blit()，并向他传递一幅图像，以及与该图像相关联的 rect 对象，从而在屏幕上绘制文本图像。至此，Button 类变创建好了。





#### 2.在屏幕上绘制按钮

使用 Button 类来创建一个 Play 按钮。鉴于只需要一个 Play 按钮，直接在 alien_invasion.py 中创建，如下所示：

**alien_invasion.py**

```python
--snip--
from game_stats import GameStats
from button import Button
--snip--

def run_game():
    --snip--
    pygame.display.set_caption("Alien Invasion")
    
    #创建 Play 按钮
    play_button = Button(ai_settings, screen, "play")
    --snip--
    
    #开始游戏主循环
    while True:
        --snip--
        gf.update_screen(ai_settings, screen, stats, 
                         ship, aliens, bullets, play_button)
        
run_game()

```

* 导入 Button 类，并创建一个名为 play_button 的实例（第11行代码），然后将 play_button 传递给 update_screen()，以便能够在屏幕更像时显示按钮（第17行代码）。



接下来，修改 update_screen()，以便在游戏处于非活动状态时显示 Play 按钮：

**game_functions.py**

```python
def update_screen(ai_settings, screen, stats, ship, 
                  aliens, bullets, play_button):
    """更新屏幕上的图像，并切换到新屏幕"""
    --snip--
    
    #如果游戏处于非活动状态，就绘制 Play 按钮
    if not stats.game_active:
        play_button.draw_button()
        
    #让最近绘制的屏幕可见
    pygame.display.flip()

```

为让 Play 按钮位于其他所有屏幕元素上面，在绘制其他所有元素后再绘制这个按钮，然后切换到新屏幕。

现在运行游戏，将在屏幕中央看到一个 Play 按钮。

![1680781402292](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1680781402292.png)





#### 3.开始游戏

为在玩家单击 Play 按钮时开始新游戏，需在 game_functions.py 中添加如下代码，以监视与这个按钮相关的鼠标事件：

**game_functions.py**

```python
def check_events(ai_settings, screen, stats, play_button, ship, bullets):
    """响应按键和鼠标事件"""
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            --snip--
        elif event.type == pygame.MOUSEBUTTONDOWN:
            mouse_x, mouse_y = pygame.mouse.get_pos()
            check_play_button(stats, play_button, mouse_x, mouse_y)
           
def check_play_button(stats, play_button, mouse_x, mouse_y):
    """在玩家单击 Play 按钮时开始新游戏"""
    if play_button.rect.collidepoint(mouse_x, mouse_y):
        stats.game_active = True

```

* 修改了 check_events() 的定义，在其中添加了形参 stats 和 play_button。将使用 stats 来访问标志 play_button 来检查玩家是否单击了 Play 按钮。



* 无论玩家单击屏幕什么地方，Pygame 都将检测到一个 MOUSEBUTTONDOWN 事件（第6行行代码），但仅需要游戏在玩家用鼠标单击 Play 按钮时作出响应。



* 第7行代码行，使用了 pygame.mouse.get_pos()，它返回了一个元组，其中包含玩家单击时鼠标的鼠标的 x 和 y 坐标。将这些值传递给函数 check_play_button()（第8行代码），而这个函数使用 collidepoint() 检查鼠标单击位置是否在 Play 按钮的 rect 内（第12行代码）。如果是这样，就将 game_active 设为 True，让游戏就此开始！



在 alien_invasion.py 中调用 check_events()，需要传递另外两个实参——stats 和 play_button：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, stats, 
                        play_button, ship, bullets)
        --snip--

```

至此，可以开始游戏了。游戏结束时，game_active 应为 False，并重新显示 Play 按钮。

![1680781976735](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1680781976735.png)





#### 4.重置游戏

前面编写的代码只处理了玩家第一次单击 Play 按钮的情况，而没有处理游戏结束，因为没有重置导致游戏结束的条件。

为在玩家每次单击 Play 按钮时都重置游戏，需要重置统计信息、删除现有的外星人和子弹、创建一群新的外星人，并让飞船居中，如下所示：

**game_functions.py**

```python
def check_play_button(ai_settings, screen, stats, 
                      play_button, ship, aliens, bullets, 
                      mouse_x, mouse_y):
    """在玩家单击 Play 按钮时开始新游戏"""
    if play_button.rect.collidepoint(mouse_x, mouse_y):
        #重置游戏统计信息
        stats.reset_stats()
        stats.game_active = True
        
        #清空外星人列表和子弹列表
        aliens.empty()
        bullets.empty()
        
        #创建一群新的外星人，并让飞船居中
        create_fleet(ai_settins, screen, ship, aliens)
        ship.cente_ship()

```

* 更新了 check_play_button() 的定义，使其能够访问 ai_settings、stats、ship、aliens 和 bullets。为重置在游戏期间发生了变化的设置以及刷新游戏的视觉元素，它需要这些对象。



* 第7行代码行，重置了游戏统计信息，给玩家提供了三艘新飞船。接下来，将 game_active 设置为 True（这样，这个函数的代码执行完毕后，游戏就会开始），清空编组 aliens 和 bullets（第11、12行代码），创建一群新外星人，并将飞船居中（第15、16行代码）。



check_events() 的定义需要修改，调用 check_play_button() 的代码亦如此：

**game_functions.py**

```python
def check_events(ai_settings, screen, stats, play_button, 
                 ship, aliens, bullets):
    """响应按键和鼠标事件"""
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            --snip--
        elif event.type == pygame.MOUSEBUTTONDOWN:
            mouse_x, mouse_y = pygame.mouse.get_pos()
            check_play_button(ai_settings, screen, stats,
                             play_button, ship, aliens, bullets,
                             mouse_x, mouse_y)

```

* check_events() 的定义需要形参 aliens，以便将它传递给 check_play_button()。接下来，修改了调用 check_play_button() 的代码，以将合适的实参传递给它（第9行代码）。



下面来修改 alien_invasion.py 中调用 check_events() 的代码，已将实参 aliens 传递给它：

**alien_invasion.py**

```python
	#开始游戏
    while True:
        gf.check_events(ai_settings, screen, stats, play_button, 
                        ship, aliens, bullets)
        --snip--

```

现在，每当玩家单击 Play 按钮时，这个游戏都将正确的重置，让玩家想玩多少次就玩多少次！





#### 5.将 Play 按钮切换到非活动状态

当前，Play 按钮存在一个问题，那就是即使 Play 按钮不可见，玩家单击其原来所在区域时，游戏依然会作出响应。游戏开始后，如果玩家不小心单击了 Play 按钮原来所处的区域，游戏将重新开始！

为修复这个问题，可让游戏仅在 game_active 为 False 时才开始：

**game_functions.py**

```python
def check_play_button(ai_settings, screen, stats, play_button,
                     ship, aliens, bullets,
                     mouse_x, mouse_y):
    """ 玩家单击 Play 按钮时开始新游戏"""
    button_clicked = play_button.rect.collidepoint(mouse_x, mouse_y)
    if button_clicked and not stats.game_active:
        #重置游戏统计信息
        --snip--

```

* 标志 button_clicked 的值为 True 或 False（第5行代码），仅当玩家单击了 Play 按钮且游戏当前处于非活动状态时，游戏才重新开始（第6行代码）。

  为测试这种行为，可开始新游戏，并不断的单击 Play 按钮原来所在的区域。如果一切都想预期那样工作，单击 Play 按钮原来所在区域应该没有任何影响。





#### 6.隐藏光标

为让玩家能够开始游戏，要让光标可见，但游戏开始后，光标只会添乱。为修复这种问题，在游戏处于活动状态时让光标不可见：

**game_functions.py**

```python
def check_play_button(ai_settings, screen, stats, play_button, 
                      ship, aliens, bullets,
                      mouse_x, mouse_y):
    """在玩家单击 Play 按钮时开始新游戏"""
    button_clicked = play_button.rect.collidepoint(mouse_x, mouse_y)
    if button_clicked and not stats.game_active:
        #隐藏光标
        pygame.mouse.set_visible(False)
        --snip--

```

通过向 set_visible() 传递 False，让 Pygame 在光标位于游戏窗口内时将其隐藏起来。

游戏结束后，将重新显示光标，让玩家能够单击 Play 按钮来开始游戏。相关代码如下：

**game_functions.py**

```python
def ship_hit(ai_settings, screen, stats, ship, aliens, bullets):
    """响应飞船被外星人撞到"""
    if stats.ships_left > 0:
        --snip--
        
    else:
        stats.game_active = False
        pygame.mouse.set_visible(True)

```

在 ship_hit() 中，在游戏进入非活动状态后，立即让光标可见。关注这样的细节让游戏显得更专业，也让玩家能够专注于玩游戏而不是费力搞明白用户界面







### 二、提高等级

当前，将整群外星人都消灭干净后，玩家将提高一个等级，但游戏的难度并没有变。下面来增加一点趣味性：每当玩家将屏幕上的外星人都消灭干净后，加快游戏的节奏，让游戏玩起来更难。

#### 1.修改速度设置

首先重新组织 Settings 类，将游戏设置划分成静态和动态两组。对于随着游戏进行而变化的设置，还确保它们在开始游戏时被重置。settings.py 的方法 __ init __ () 如下：

**settings.py**

```python
	def __init__(self):
        """初始化游戏的静态设置"""
        #屏幕设置
        self.screen_width = 1200
        self.screen_height = 800
        self.bg_color = (230, 230, 230)
        
        #飞船设置
        self.ship_limit = 3
        
        #子弹设置
        self.bullet_width = 3
        self.bullet_height = 15
        self.bullet_color = 60, 60, 60
        self.bullets_allowed = 3
        
        #外星人设置
        self.fleet_drop_speed = 10
        
        #以什么样的速度加快游戏节奏
        self.speedup_scale = 1.1
        
        self.initialize_dynamic_settings()

```

* 依然在 __ init __() 中初始化静态设置。第21行代码处，添加了设置 speedup_scale，用于控制游戏节奏的加快速度：2 表示玩家每提升一个等级，游戏的节奏就翻倍；1 表示游戏节奏始终不变。



* 第23行代码行，调用 Initialize_dynamic_settings()，以初始化随游戏进行而变化的属性。



其中的代码如下：

**settings.py**

```python
	def initialize_dynamic_settings(self):
        """初始化随游戏进行而变化的设置"""
        self.ship_speed_factor = 1.5
        self.bullet_speed_factor = 3
        self.alien_speed_factor = 1
        
        #fleet_direction 为 1 表示向右；为 -1 表示向左
        self.fleet_direction = 1

```

这个方法设置了飞船、子弹和外星人的初始速度。随游戏的进行，将提高这些速度，而每当玩家开始新的游戏时，都将重置这些速度。

在这个方法中，还设置了 fleet_direction，使得游戏刚开始时，外星人总是向右移动。每当玩家提高一个等级时，都使用 increase_speed() 来提高飞船、子弹和外星人的速度：

**settings.py**

```python
	def increase_speed(self):
        """提高速度设置"""
        self.ship_speed_factor *= self.speedup_scale
        self.bullet_speed_factor *= self.speedup_scale
        self.alien_speed_factor *= self.speedup_scale

```

为提高这些游戏元素的速度，将每个速度设置都乘以 speedup_scale 的值。

在 check_bullet_alien_collisions() 中，在整群外星人都被消灭后调用 increase_speed() 来加快游戏的节奏，再创建一群新的外星人：

**game_functions.py**

```python
def check_bullet_alien_collisions(ai_settings, screen, ship, aliens, bullets):
    if len(aliens) == 0:
        #删除现有的子弹，加快游戏节奏，并创建一群新的外星人
        bullets.empty()
        ai_settings.increase_speed()
        create_fleet(ai_settings, screen, ship, aliens)

```

通过修改速度设置 ship_speed_factor、alien_speed_factor 和 bullet_speed_factor 的值，足以加快整个游戏的节奏。





#### 2.重置速度

每当玩家开始新游戏时，都需要将发生了变化的设置重置为初始值，否则新游戏开始时，速度设置将是前一次游戏增加了的值：

**game_functions.py**

```python
def check_play_button(ai_settings, screen, stats, play_button, ship, aliens, bullets, mouse_x, mouse_y):
    """在玩家单击 Play 按钮时开始新游戏"""
    button_clicked = play_button.rect.collidepoint(mouse_x, mouse_y)
    if button_clicked and not stats.game_active:
        #重置游戏设置
        ai_settings.initialize_dynamic_settings()
        
        #隐藏光标
        pygame.mouse.set_visible(False)
        --snip--

```

现在，游戏更有趣，也更有挑战性了。每当玩家将屏幕上的外星人消灭干净后，游戏都将加快节奏，因此难度会加大，可根据需要调整游戏难度。







### 三、统计分数

接下来实现一个积分系统，以实时的跟踪玩家的得分，并显示最高得分、当前等级和余下的飞船数。

得分是游戏的一项统计信息，因此在 GameStats 中添加一个 score 属性：

**game_stats.py**

```python
clss GameStats():
    --snip--
    def reset_stats(self):
        """初始化随游戏进行可能变化的统计信息"""
        self.ships_left = self.ai_settings.ship_limit
        self.score = 0

```

为在每次开始游戏时都重置得分，在 reset_stats() 而不是 __ init __() 中初始化 score。

#### 1.显示得分

为在屏幕上显示得分，首先创建一个新类 Scoreboard。就当前而言，这个类只显示当前得分，但后面也将使用它来显示最高得分、等级和余下的飞船数。

下面是类的前半部分，被保存为文件 scoreboard.py：

**scoreboard.py**

```python
import pygame.font

class Scoreboard():
    """显示得分信息的类"""
    
    def __init__(self, ai_settings, screen, stats):
        """初始化显示得分涉及的属性"""
        self.screen = screen
        self.screen_rect = screen.get_rect()
        self.ai_settings = ai_settings
        self.stats = stats
        
        #显示得分信息时使用的字体设置
        self.text_color = (30, 30, 30)
        self.font = pygame.font.SysFont(None, 48)
        
        #准备初始得分图像
        self.prep_score()

```

* 由于 Scoreboard 在屏幕上显示文本，因此首先导入模块 pygame.font。



* 接下来，在 __ init __() 中包含形参 ai_settings、screen 和 stats，让她能够报告跟踪的值（第6行代码）。



* 然后，设置文本颜色（第14行代码）并实例化一个字体对象（第15行代码）。



为将要显示的文本转换为图像，调用了 prep_score()（第18行代码），其定义如下：

**scoreboard.py**

```python
	def prep_score(self):
        """将得分转化为一幅渲染的图像"""
        score_str = str(self.stats.score)
        self.score_image = self.font.render(score_str, True, self.text_color, self.ai_settings.bg_color)
        
        #将得分放在屏幕右上角
        self.score_rect = self.score_image.get_rect()
        self.score_rect.right = self.screen_rect.right - 20
        self.score_rect.top = 20

```

* 在 prrp_score() 中，首先将数字值 stats.score 转化为字符串（第3行代码），再将这个字符串传递给创建图像的 render()（第4行代码）。为在屏幕上清晰的显示得分，向 render() 传递了屏幕背景色，以及文本颜色。



* 将得分放在屏幕右上角，并在得分增大导致这个数字更宽时让它向左延伸。为确保得分始终锚定在屏幕右边，创建了一个名为 score_rect 的 rect（第7行代码），让其右边缘与屏幕右边缘相距 20 像素（第8行代码），并让其上边缘与屏幕上边缘也相距 20 像素（第9行代码）。



最后，创建方法 show_score()，用于显示渲染好的得分图像：

**scoreboard.py**

```python
	def show_score(self):
        """在屏幕上显示得分"""
        self.screen.blit(self.score_image, self.score_rect)

```

这个方法将得分图像显示到屏幕上，并将其放在 score_rect 指定位置。





#### 2.创建记分牌

为显示得分，在 alien_invasion.py 中创建一个 Scoreboard 实例：

**alien_invasion.py**

```python
--snip--
from game_stats import GameStats
from scoreboard import Scoreboard
--snip--
def run_game():
    --snip--
    #创建 Play 按钮
    Play_button = Button(ai_settings, screen, "play")
    
    #创建存储游戏统计信息的实例，并创建记分牌
    stats = GameStats(ai_settings)
    sb = Scoreboard(ai_settings, screen, stats)
    --snip--
    #开始游戏主循环
    while True:
        --snip--
        gf.update_screen(ai_settings, screen, stats, sb, ship, aliens, bullets, play_button)
        
run_game()

```

* 导入新创建的类 Scoreboard，并在创建实例 stats 后创建了一个名为 sb 的 Scoreboard 的实例（第9行代码）。



* 接下来，将 sb 传递给 update_screen()，让它能够在屏幕上显示得分（第14行代码）。



为显示得分，将 update_screen() 修改成如下：

**game_functions.py**

```python
def update_screen(ai_settings, screen, stats, sb, ship, aliens, bullets, play_button):
    --snip--
    #显示得分
    sb.show_score()
    
    #如果游戏处于非活动状态，就显示 Play 按钮
    if not stats.game_active:
        play_button.draw_button()
        
    #让最近绘制的屏幕可见
    pygame.display.flip()

```

在 update_screen() 的形参列表中添加了 sb，并在绘制 Play 按钮前调用 show_score。

若现在运行游戏，会在屏幕右上角显示 0。

![1680785940168](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1680785940168.png)





#### 3.在外星人被消灭时更新得分

为在屏幕上实时的显示得分，每当有外星人被击中时，都更新 stats.score 的值，再调用 prep_score() 更新得分图像。但在此之前，需要指定玩家每击落一个外星人都将得到多少个点：

**settings.py**

```python
	def initalize_dynamic_settings(self):
        --snip--
        
        #计分
        self.alien_points = 50

```

随着游戏的进行，将提高每个外星人值的点数。为确保每次开始新游戏时这个值都会被重置，在 initialize_dynamic_settings() 中设置它。

在 check_bullet_alien_collision() 中，每当有外星人被击落时，都更新得分：

**game_functions.py**

```python
def check_bullet_alien_collisions(ai_settings, screen, stats, sb, ship, aliens, bullets):
    """响应子弹和外星人发生碰撞"""
    #删除发生碰撞的子弹和外星人
    collisions = pygame.sprite.groupcollide(bullets, aliens, True, True)
    
    if collisions:
        stats.score += ai_settings.alien_points
        sb.prep_score()
    --snip--

```

更新 check_bullet_alien_collisions() 的定义，在其中包含了形参 stats 和 sb，让它能够更新得分和记分牌。



* 有子弹撞到外星人时，Pygame 返回一个字典（collisions）。检查这个字典是否存在，如果存在，就将得分加上一个外星人值的点数（第7行代码）。



* 接下来，调用 prep_score() 来创建一幅显示最新得分的新图像。



需要修改 update_bullets()，确保在函数之间传递合适的实参：

**game_functions.py**

```python
def update_bullets(ai_settings, screen, stats, sb, ship, aliens, bullets):
    """更新子弹的位置，并删除已消失的子弹"""
    --snip--
    
    check_bullet_alien_collisions(ai)settings, screen, stats, sb, ship, aliens, bullets)

```

在 update_bullets() 的定义中，需要新增形参 stats 和 sb，而调用 check_bullet_alien_collisions() 时，也需要传递实参 stats 和 sb。

还需要修改主循环 while 中调用 update_bullets() 的代码：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, stats, play_button, ship, aliens, bullets)
        if stats.game_active:
            ship.update()
            gf.update_bullets(ai_settings, screen, stats, sb, ship, aliens, bullets)
            --snip--

```

调用 update_bullets() 时，需要传递实参 stats 和 sb。

若现在运行游戏，得分将不断增加！

![1680788055139](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1680788055139.png)





#### 4.将消灭的每个外星人的点数都计入得分

当前，代码可能遗漏了一些被消灭的外星人。例如，如果在一次循环中有两颗子弹射中了外星人，或者因子弹更宽而同时击中了多个外星人，玩家将只能得到一个被消灭的外星人的点数。为修复这种问题，来调整检测子弹和外星人碰撞的方式。

在 check_bullet_alien_collisions() 中，与外星人碰撞的子弹都是字典 collisions 中的一个键；而与每颗子弹相关的值都是一个列表，其中包含该子弹撞到的外星人。遍历字典 collisions，确保将消灭的每个外星人的点数都计入得分：

**game_functions.py**

```python
def check_bullet_alien_collisions(ai_settings, screen, stats, sb, ship, aliens, bullets):
    --snip--
    if collisions:
        for aliens in collisions.values():
            stats.score += ai_settings.alien_points * len(aliens)
            sb.prep_score()
	--snip--

```

如果字典 collisions 存在，就遍历其中的所有值。别忘了，每个值都是一个列表，包含被同一颗子弹击中的所有外星人。

对于每个列表，都将一个外星人的点数乘以其中包含的外星人数量，并将结果加入到当前得分中。为测试这一点，请将子弹宽度设置改为 300 像素，并核实你得到了更宽的子弹击中的每个外星人的点数，再将子弹宽度恢复。





#### 5.提高分值

玩家每提高一个等级，游戏都变得更难，因此处于较高的等级时，击杀外星人得分应更高。为实现这种功能，添加一些代码，以在游戏节奏加快时提高分值：

**settings.py**

```python
class Settings():
    """存储游戏《外星入侵》的所有设置的类"""
    
    def __init__(self):
        --snip--
        #加快游戏节奏的速度
        self.speedup_scale = 1.1
        #外星人点数的提高速度
        self.score_scale = 1.5
        
        self.initialize_dynamic_settings()
        
    def increase_speed(self):
        """提高速度设置和外星人分值"""
        self.ship_speed_factor *= self.speedup_scale
        self.bullet_speed_factor *= self.speedup_scale
        self.alien_speed_factor *= self.speedup_scale
        
        self.alien_points = int(self.alien_points * self.score_scale)

```

* 定义了点数提高的速度，并称之为 score_scale（第9行代码）。现在，加快游戏节奏的同时，提高了每个外星人的分值。为让分值为整数，使用了函数 int()。



为显示外星人的分值，在 Settings 的方法 increase_speed() 中添加一天 print 语句：

**settings.py**

```python
	def increase_speed(self):
        --snip--
        self.alien_points = int(self.alien_points * self.score_scale)
        print(self.alien_points)

```

现在每提高一个等级时，都会在终端窗口看到新的点数值。

![1680789225619](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1680789225619.png)





#### 6.将得分圆整

大多数街机风格的射击游戏都将得分显示为 10 的整数倍，下面让计分系统遵循这个原则。还将设置得分的格式，在大数字中添加逗号表示千位分隔符。

在 Scoreboard 中执行这种修改：

**scoreboard.py**

```python
	def prep_score(self):
        """将得分转化为渲染的图像"""
        rounded_score = int(round(self.stats.score, -1))
        score_str = "{:,}".format(rounded_score)
        self.score_image = self.font.render(score_str, True, self.text_color, self.ai_settings.bg_color)
        --snip--

```

* 函数 round() 通常让小数精确到小数点后多少位，其中小数位数是由第二个实参指定的。然而，如果将第二个实参指定为负数，round() 将圆整到最近的 10、100、1000 等整数倍。

>注意：在 python 2.7 中，round() 总是返回一个小数值，因此使用 int() 确保报告得分为整数。若是 python 3，可省略对 int() 的调用。
>
>第4行代码行，使用了一个字符串格式设置指令，让 python 将数值转化为字符串时在其中作为千位符。







#### 7.最高得分

每个玩家都想超过游戏的最高得分记录。下面来跟踪并显示最高得分，给玩家提供要超越的目标。将最高分存储在 GameStats 中：

**game_stats.py**

```python
	def __init__(self, ai_settings):
        --snip--
        #在任何情况下都不应重置最高得分
        self.high_score = 0

```

鉴于在任何情况下都不会重置最高得分，在 __ init __() 中而不是 reset_stats() 中初始化 high_score。

下面来修改 Scoreboard 以显示最高得分。先来修改 __ init __():

**scoreboard.py**

```python
	def __init__(self, ai_settings, screen, stats):
        --snip--
        
        #显示得分信息时使用的字体设置
        self.text_color = (30, 30, 30)
        self.font = pygame.font.SysFont(None, 48)
        
        #准备包含最高得分和当前得分的图像
        self.prep_score()
        self.prep_high_score()

```

最高得分将与当前得分分开显示，因此需要编写一个新方法 prep_high_score()，用于准备包含最高得分的图像（第5行代码）。

方法 prep_high_score() 的代码如下：

**scoreboard.py**

```python
	def prep_high_score(self):
        """将最高得分转化为渲染的图像"""
        high_score = int(round(self.stats.high_score, -1))
        high_score_str = "{:,}".format(high_score)
        self.high_score_image = self.font.render(high_score_str, True, self.text_color, self.ai_settings.bg_color)
        
        #将最高得分放在屏幕顶部中央
        self.high_score_rect = self.high_score_image.get_rect()
        self.high_score_rect.centerx = self.screen_rect.centerx
        self.high_score_rect.top = self.score_rect.top

```

* 将最高得分圆整到最近的 10 的整数倍（第3行代码），并添加了用逗号表示的千分位分隔符（第4行代码）。



* 然后，根据最高得分生成一幅图像（第5行代码），使其水平居中（第9行代码），并将其 top 属性设置为当前得分图像的 top 属性（第10行代码）。



现在，方法 show_score() 需要在屏幕右上角显示当前得分，并在屏幕顶部中央显示最高得分：

**scoreboard.py**

```python
	def show_score(self):
        """在屏幕上显示当前得分和最高得分"""
        self.screen.blit(self.score_image, self.score_rect)
        self.screen.blit(self.high_score_image, self.high_score_rect)

```

为检查是否诞生了新的最高得分，在 game_functions.py 中添加一个新函数 check_high_score()：
**game_functions.py**

```python
def check_high_score(stats, sb):
    """检查是否诞生了新的最高得分"""
    if stats.score > stats.high_score:
        stats.high_score = stats.score
        sb.prep_high_score()

```

* 函数 check_high_score() 包含两个形参：stats 和 sb。它使用 stats 来比较当前得分和最高得分，并咋必要时使用 sb 来修改最高得分图像。



* 第3行代码行，比较当前得分和最高得分，如果当前得分更高，就更新 high_score 的值，并调用 prep_high_score() 来更新包含最高得分的图像。



在 check_bullet_alien_collisions() 中，没当有外星人被消灭，都需要在更新得分后调用 check_high_score()：

**game_functions.py**

```python
def check_bullet_alien_collisions(ai_settings, screen, stats, sb, ship, aliens, bullets):
    --snip--
    if collisions:
        for aliens in collisions.values():
            stats.score += ai_settings.alien_points * len(aliens)
            sb.prep_score()
        check_high_score(stats, sb)
    --snip--
    
```

字典 collisions 存在时，根据消灭了多少外星人来更新得分，再调用 check_high_score。

第一次玩的分数，就是最高分。之后再次开始，游戏正上方中央数字为当前最高分。

![1680790214792](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1680790214792.png)





#### 8.显示等级

为在游戏中显示玩家的等级，首先需要在 GameStats 中添加一个表示当前等级的属性。为确保每次开始游戏都重置等级，在 reset_stats() 中初始化它：

**game_stats.py**

```python
	def reset_stats(self):
        """初始化随游戏进行可能变化的统计信息"""
		self.ships_left = self.ai_settings.ship_limit
        self.score = 0
        self.level = 1

```

为让 Scoreboard 能够在当下得分下方显示当前等级，在 __ init __() 中调用了一个新方法 prep_level()：

**scoreboard.py**

```python
	def __init__(self, ai_settings, screen, stats):
        --snip--
        #显示得分信息时使用的字体设置
        self.text_color = (30, 30, 30)
        self.font = pygame.font.SysFont(None, 48)
        
        #准备包含得分的初始化图像
        self.prep_score()
        self.prep_high_score()
        self.prep_level()

```

prep_level() 的代码如下：

**scoreboard.py**

```python
	def prep_level(self):
        """将等级转化为渲染的图像"""
        self.level_image = self.font.render(str(self.stats.level), True, self.text_color, self.ai_settings.bg_color)
        
        #将等级放在得分下方
        self.level_rect = self.level_image.get_rect()
        self.level_rect.right = self.score_rect.right
        self.level_rect.top = self.score_rect.bottom + 10

```

* 方法 prep_level() 根据存储在 stats.level 中的值创建一幅图像（第3行代码），并将其 right 属性设置为得分的 right 属性（第7行代码）。



* 然后，将 top 属性设置为比得分图像的 bottom 属性大 10 像素，以便在得分和等级之间留出一定的空间（第8行代码）。



还需要更新 show_score()：

**scoreboard.py**

```python
	def show_score(self):
        """在屏幕上显示飞船得分"""
        self.screen.blit(self.score_image, self.score_rect)
        self.screen.blit(self.high_score_image, self.high_score_rect)
        self.screen.blit(self.level_image, self.level_rect)

```

在这个方法中，添加了一行在屏幕上显示等级图像的代码。

在 check_bullet_alien_collisions() 中提高等级，并更新等级图像：

**game_functions.py**

```python
def check_bullet_alien_collisions(ai_settings, screen, stats, sb, ship, aliens, bullets):
    if len(aliens) == 0:
        #如果整群外星人都被消灭，就提高一个等级
        bullets.empty()
        ai_settings.increase_speed()
        
        #提高等级
        stats.level += 1
        sb.prep_level()
        
        create_fleet(ai_settings, screen, ship, aliens)

```

* 如果整群外星人都被消灭，就将 stats.level 的值加 1（第8行代码），并调用 prep_level() ，以确保正确的显示新等级（第9行代码）。



为确保开始新游戏时更新计分和等级图像，在按钮 Play 被单击时触发重置：

**game_functions.py**

```python
def check_play_button(ai_settings, screen, stats, sb, play_button, ship, aliens, bullets, mouse_x, mouse_y):
    """在玩家单击 Play 按钮时开始新游戏"""
    button_clicked = play_button.rect.collidepoint(mouse_x, mouse_y)
    if button_clicked and not stats.game_active:
        --snip--
        
        #重置游戏统计信息
        stats.reset_stats()
        stats.game_active = True
        
        #重置记分牌图像
        sb.prep_score()
        sb.prep_hight_score()
        sb.prep_level()
        
        #清空外星人列表和子弹列表
        aliens.empty()
        bullets.empty()
        
        --snip--

```

* check_play_button() 的定义需要包含对象 sb。为重置记分牌图像，在重置相关游戏设置后调用 prep_score()、prep_high_score() 和 prep_level()（第12行代码）。



在 check_events() 中，现在需要向 check_play_button() 传递 sb，让它能够访问记分牌对象：

**game_functions.py**

```python
def check_events(ai_settings, screen, stats, sb, play_button, ship, aliens, bullets):
    """响应按键和鼠标事件"""
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            --snip--
        elif event.type == pygame.MOUSEBUTTONDOWN:
            mouse_x, mouse_y = pygame.mouse.get_pos()
            check_play_button(ai_settings, screen, stats, sb, play_button, ship, aliens, bullets, mouse_x, mouse_y)

```

* check_events() 的定义需要包含形参 sb，这样调用 check_play_button() 时，才能够 sb 作为实参传递给它（第8行代码）。



最后，更新 alien_invasion.py 中调用 check_events() 的代码，也向它传递 sb：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        gf.check_events(ai_settings, screen, stats, sb, play_button, ship, aliens, bullets)
        --snip--

```

运行游戏，看看你能够升到多少级。

> 注意：在一些经典游戏中，得分带标签，如 Score、High Score 和 Level。没有显示这些标签，因为在开始玩这款游戏后，数字含义一目了然。要包含这些标签，只需在 Scoreboard 中调用 font.render() 前，将它们添加到得分字符串中即可。

![1680790984575](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1680790984575.png)





#### 9.显示余下的飞船数

最后，来显示玩家还有多少艘飞船，但使用图形而不是数字。为此，在屏幕左上方绘制飞船图像来指出还余下多少艘飞船，就像众多经典的街机游戏那样。

首先，需要让 Ship 继承 Sprite，以便能够创建飞船编组：

**ship.py**

```python
import pygame
from pygame.sprite import Sprite

class Ship(Sprite):
    
    def __init__(self, ai_settings, screen):
        """初始化飞船，并设置其起始位置"""
        super(Ship, self).__init__()
        --snip--

```

* 导入了 Sprite，让 Ship 继承 Sprite（第2行代码），并在 __ init __() 的开头就调用了 super()（第6行代码）。



接下来，需要修改 Scoreboard，在其中创建一个可供显示的飞船编组。下面是其中的 import 语句和方法 __ init __()：

**scoreboard.py**

```python
import pygame.font
from pygame.sprite import Group

from ship import Ship

class Scoreboard():
    """报告得分信息的类"""
    
    def __init__(self, ai_settings, screen, stats):
        --snip--
        self.prep_level()
        self.prep_ships()
        --snip--

```

鉴于要创建一个飞船编组，导入 Group 和 Ship 类。调用 prep_level() 后，调用了 prep_ships()。

prep_ships() 的代码如下：

**scoreboard.py**

```python
	def prep_ships(self):
        """显示还余下多少艘飞船"""
        self.ships = Group()
        for ship_number in range(self.stats.ships_left):
            ship = Ship(self.ai_settings, self.screen)
            ship.rect.x = 10 + ship_number * ship.rect.width
            ship.rect.y = 10
            self.ships.add(ship)

```

* 方法 prep_ships() 创建一个空编组 self.ships，用于存储飞船实例（第3行代码）。



* 为填充这个编组，根据玩家还有多少艘飞船运行一个循环相应的次数（第4行代码）。



* 在这个循环中，创建一艘新飞船，并设置其 x 坐标，让整个飞船编组都位于屏幕左边，且每艘飞船的左边距都为 10 像素（第6行代码）。



* 还将 y 坐标设置为离屏幕上边缘 10 像素，让所有飞船都与得分图像对其（第7行代码）。最后将每艘新飞船都添加到编组 ships 中（第8行代码）。



现在需要在屏幕上绘制飞船了：

**scoreboard.py**

```python
	def show_score(self):
        --snip--
        self.screen.blit(self.level_image, self.level_rect)
        #绘制飞船
        self.ships.draw(self.screen)

```

为在屏幕上显示飞船，对编组调用了 draw()。pygame 将绘制每艘飞船。

为子啊游戏开始时让玩家知道有多少飞船，在开始新游戏时调用 prep_ships()。这是在 game_functions.py 的 check_play_button() 中进行的：

**game_functions.py**

```python
def check_play_button(ai_settings, screen, stats, sb, play_button, ship, aliens, bullets, mouse_x, mouse_y):
    """在玩家单击 Play 按钮时开始新游戏"""
    button_clicked = play_button.rect.collidepoint(mouse_x, mouse_y)
    if button_clicked and not stats.game_active:
        --snip--
        #重置记分牌图像
        sb.prep_score()
        sb.prep_high_score()
        sb.prep_level()
        sb.prep_ships()
        --snip--

```

还在飞船被外星人撞到时调用 prep_ships()，从而在玩家损失一艘飞船时更新飞船图像：

**game_functions.py**

```python
def update_aliens(ai_settings, screen, stats, sb, ship, aliens, bullets):
    --snip--
    #检测外星人和飞船之间的碰撞
    if pygame.sprite.spritecollideany(ship, aliens):
        ship_hit(ai_settings, screen, stats, sb, ship, aliens, bullets)
        #检测是否有外星人抵达屏幕底端
        check_aliens_bottom(ai_settings, screen, stats, sb, ship, aliens, bullets)
        
def ship_hit(ai_settings, screen, stats, sb, ship, aliens, bullets):
    """响应被外星人撞到的飞船"""
    if stats.ships_left > 0:
        #将 ships_left 减 1
        stats.ships_left -= 1
        
        #更新记分牌
        sb.prep_ships()
        
        #清空外星人列表和子弹列表
        --snip--

```

* 首先，在 update_aliens() 的定义中添加了形参 sb（第1行代码）。然后，向 ship_hit()（第5行代码）和 check_aliens_bottom()（第8行代码）都传递了 sb，让它们都能访问记分牌对象。



* 接下来，更新了 ship_hit() 的定义，使其包含形参 sb（第9行代码）。在将 ships_left 的值减 1 后调用了 prep_ships()（第16行代码），这样每次损失了飞船时，显示的飞船数都是正确的。



在 check_aliens_bottom() 中需要调用 ship_hit()，因此对这个函数进行更新：

**game_functions.py**

```python
def check_aliens_bottom(ai_settings, screen, stats, sb, ship, aliens, bullets):
    """检测是否有外星人抵达屏幕底端"""
    screen_rect = screen.get_rect()
    for alien in aliens.sprites():
        if alien.rect.bottom >= screen_rect.bottom:
            #像飞船被外星人撞到一样处理
            ship_hit(ai_settings, screen, stats, sb, ship, aliens, bullets)
            break

```

现在，check_aliens_bottom() 包含形参 sb，并在调用 ship_hit() 时传递了实参 sb。

最后，在 alien_invasion.py 中修改调用 update_aliens() 的代码，向它传递实参 sb：

**alien_invasion.py**

```python
	#开始游戏主循环
    while True:
        --snip--
        if stats.game_active:
            ship.update()
            gf.upda_bullets(ai_settings, screen, stats, sb, ship, aliens, bullets)
            gf.update_aliens(ai_settings, screen, stats, sb, ship, aliens, bullets)
            --snip--

```

运行游戏，可以看到余下多少飞船。

![1680791959231](C:\Users\弑神者\AppData\Roaming\Typora\typora-user-images\1680791959231.png)







## 完整代码包

* 使用 python3.8 版本
* 共用 9 个代码文件（.py），按照首字母排序为：alien.py, alien_invasion.py, bullet.py, button.py, game_functions.py, game_stats.py, scoreboard.py, settings.py, ship.py
* 1 个图片文件夹（images）

**alien.py**

```python
import pygame
from pygame.sprite import Sprite

class Alien(Sprite):
    """表示单个外星人的类"""

    def __init__(self, ai_settings, screen):
        """初始化外星人并设置其起始位置"""
        super(Alien, self).__init__()
        self.screen = screen
        self.ai_settings = ai_settings

        #加载外星人图像，并设置其 rect 属性
        self.image = pygame.image.load('images/alien.bmp')
        self.rect = self.image.get_rect()

        #每个外星人最初都在屏幕左上角附件
        self.rect.x = self.rect.width
        self.rect.y = self.rect.height

        #存储外星人的准确位置
        self.x = float(self.rect.x)

    def blitme(self):
        """在指定位置绘制外星人"""
        self.screen.blit(self.image, self.rect)

    def check_edges(self):
        """如果外星人位于屏幕边缘，就返回 True"""
        screen_rect = self.screen.get_rect()
        if self.rect.right >= screen_rect.right:
            return True
        elif self.rect.left <= 0:
            return True

    def update(self):
        """向左或向右移动外星人"""
        self.x += (self.ai_settings.alien_speed_factor *
                   self.ai_settings.fleet_direction)
        self.rect.x = self.x


```



**alien_invasion.py**

```python
import pygame
from pygame.sprite import Group

from settings import Settings
from game_stats import GameStats
from scoreboard import Scoreboard
from button import Button
from ship import Ship
import game_functions as gf

def run_game():
    #初始化游戏，并创建一个屏幕对象
    pygame.init()
    ai_settings = Settings()
    screen = pygame.display.set_mode(
        (ai_settings.screen_width, ai_settings.screen_height))
    pygame.display.set_caption("Alien Invasion")

    # 创建 Play 按钮
    play_button = Button(ai_settings, screen, "play")

    #创建一个用于存储游戏统计信息的实例，并创建记分牌
    stats = GameStats(ai_settings)
    sb = Scoreboard(ai_settings, screen, stats)

    #创建一艘飞船
    ship = Ship(ai_settings, screen)

    #创建一个用于存储子弹和一个外星人的编组
    bullets = Group()
    aliens = Group()

    #创建一个外星人
    gf.create_fleet(ai_settings, screen, ship,aliens)

    #开始游戏主循环
    while True:

        #监视键盘和鼠标事件
        gf.check_events(ai_settings, screen, stats, sb,
                        play_button, ship, aliens, bullets)

        if stats.game_active:
            ship.update()
            gf.update_bullets(ai_settings, screen, stats, sb,
                              ship, aliens, bullets)
            gf.update_aliens(ai_settings, stats, sb, screen,
                            ship, aliens, bullets)

        gf.update_screen(ai_settings, screen, stats, sb, ship,
                        aliens, bullets, play_button)

run_game()

```



**bullet.py**

```python
import pygame
from pygame.sprite import Sprite

class Bullet(Sprite):
    """一个对飞船发射的子弹进行管理的类"""

    def __init__(self, ai_settings, screen, ship):
        """在飞船所处的位置创建一个子弹对象"""
        super(Bullet, self).__init__()
        self.screen = screen

        #在（0，0）处创建一个表示子弹的矩形，再设置正确的位置
        self.rect = pygame.Rect(0, 0,
                                ai_settings.bullet_width,
                                ai_settings.bullet_height)
        self.rect.centerx = ship.rect.centerx
        self.rect.top = ship.rect.top

        #存储用小数表示的子弹位置
        self.y = float(self.rect.y)

        self.color = ai_settings.bullet_color
        self.speed_factor = ai_settings.bullet_speed_factor

    def update(self):
        """向上移动子弹"""
        #更新表示子弹位置的小数值
        self.y -= self.speed_factor
        #更新表示子弹的 rect 的位置
        self.rect.y = self.y

    def draw_bullet(self):
        """在屏幕上绘制子弹"""
        pygame.draw.rect(self.screen, self.color, self.rect)
       
```



**button.py**

```python
import pygame.font

class Button():

    def __init__(self, ai_settings, screen, msg):
        """初始化按钮的属性"""
        self.screen = screen
        self.screen_rect = screen.get_rect()

        #设置按钮的尺寸和其他属性
        self.width, self.height = 200, 50
        self.button_color = (255, 0, 0)
        self.text_color = (255, 255, 255)
        self.font = pygame.font.SysFont(None, 48)

        #创建按钮的 rect 对象，并使其居中
        self.rect = pygame.Rect(0, 0, self.width, self.height)
        self.rect.center = self.screen_rect.center

        #按钮的标签只需创建一次
        self.prep_msg(msg)

    def prep_msg(self, msg):
        """将 msg 渲染为图像，并使其在按钮上居中"""
        self.msg_image = self.font.render(msg, True,
                                          self.text_color,
                                          self.button_color)
        self.msg_image_rect = self.msg_image.get_rect()
        self.msg_image_rect.center = self.rect.center


    def draw_button(self):
        #绘制一个用颜色填充的按钮，再绘制文本
        self.screen.fill(self.button_color, self.rect)
        self.screen.blit(self.msg_image, self.msg_image_rect)
        
```



**game_functions.py**

```python
import sys
from time import sleep
import pygame

from bullet import Bullet
from alien import Alien

def check_events(ai_settings, screen, stats, sb, play_button,
                 ship, aliens, bullets):
    """响应按键和鼠标事件"""
    for event in pygame.event.get():
        if event.type == pygame.QUIT:
            sys.exit()
        elif event.type == pygame.KEYDOWN:
            check_keydown_events(event, ai_settings, screen, ship, bullets)
        elif event.type == pygame.KEYUP:
            check_keyup_events(event, ship)
        elif event.type == pygame.MOUSEBUTTONDOWN:
            mouse_x, mouse_y = pygame.mouse.get_pos()
            check_play_button(ai_settings, screen, stats, sb,
                              play_button, ship, aliens,
                              bullets, mouse_x, mouse_y)

def check_high_score(stats, sb):
    """检查是否诞生了新的最高得分"""
    if stats.score > stats.high_score:
        stats.high_score = stats.score
        sb.prep_high_score()

def check_play_button(ai_settings, screen, stats, sb,
                      play_button, ship, aliens,
                      bullets, mouse_x, mouse_y):
    """在玩家单击 Play 按钮时开始新游戏"""
    button_clicked = play_button.rect.collidepoint(mouse_x,
                                                   mouse_y)
    if button_clicked and not stats.game_active:
        #重置游戏设置
        ai_settings.initialize_dynamic_settings()

        #隐藏光标
        pygame.mouse.set_visible(False)

        #重置游戏统计信息
        stats.reset_stats()
        stats.game_active = True

        #重置记分牌图像
        sb.prep_score()
        sb.prep_hight_score()
        sb.prep_level()
        sb.prep_ships()

        #清空外星人列表和子弹列表
        aliens.empty()
        bullets.empty()

        #创建一群新的外星人，并让飞船居中
        create_fleet(ai_settings, screen, ship, aliens)
        ship.center_ship()


def check_keydown_events(event, ai_settings, screen, ship, bullets):
        """响应按键"""
        if event.key == pygame.K_RIGHT:
            ship.moving_right = True
        elif event.key == pygame.K_LEFT:
            ship.moving_left = True

        elif event.key == pygame.K_SPACE:
            fire_bullet(ai_settings, screen, ship, bullets)

        elif event.key == pygame.K_q:
            sys.exit()

def check_keyup_events(event, ship):
        """响应松开"""
        if event.key == pygame.K_RIGHT:
            ship.moving_right = False
        elif event.key == pygame.K_LEFT:
            ship.moving_left = False

def update_screen(ai_settings, screen, stats, sb, ship,
                  aliens, bullets, play_button):
    """更新屏幕上的图像，并切换到新屏幕"""
    #每次循环时都重绘屏幕
    screen.fill(ai_settings.bg_color)
    # 在飞船和外星人后面重绘所有子弹
    for bullet in bullets.sprites():
        bullet.draw_bullet()
    ship.blitme()
    aliens.draw(screen)

    #显示得分
    sb.show_score()

    #如果游戏处于非活动状态，就绘制 Play 按钮
    if not stats.game_active:
        play_button.draw_button()

    # 让最近绘制的屏幕可见
    pygame.display.flip()

def update_bullets(ai_settings, screen, stats, sb,
                   ship, aliens, bullets):
    """更新子弹的位置，并删除已消失的子弹"""
    #更新子弹的位置
    bullets.update()

    #删除已消失的子弹
    for bullet in bullets.copy():
        if bullet.rect.bottom <= 0:
            bullets.remove(bullet)

    check_bullet_alien_collisions(ai_settings, screen, stats,
                                  sb, ship, aliens, bullets)

def check_bullet_alien_collisions(ai_settings, screen, stats,
                                  sb, ship, aliens, bullets):
    """响应子弹和外星人的碰撞"""
    #删除发生碰撞的子弹和外星人
    collisions = pygame.sprite.groupcollide(bullets, aliens,
                                            True, True)
    if collisions:
        for aliens in collisions.values():
            stats.score += ai_settings.alien_points * len(aliens)
            sb.prep_score()
        check_high_score(stats, sb)

    if len(aliens) == 0:
        #删除现有的子弹并新建一群外星人
        bullets.empty()
        ai_settings.increase_speed()

        #提高等级
        stats.level += 1
        sb.prep_level()

        create_fleet(ai_settings, screen, ship, aliens)

def fire_bullet(ai_settings, screen, ship, bullets):
    """如果还没有达到限制，就发射一颗子弹"""
    #创建新子弹，并将其加入到编组 bullets 中
    if len(bullets) < ai_settings.bullets_allowed:
        new_bullet = Bullet(ai_settings, screen, ship)
        bullets.add(new_bullet)

def get_number_aliens_x(ai_settings, alien_width):
    """计算每行可容纳多少个外星人"""
    available_space_x = ai_settings.screen_width - 2 * alien_width
    number_aliens_x = int(available_space_x / (2 * alien_width))
    return number_aliens_x

def get_number_rows(ai_settings, ship_height, alien_height):
    """计算屏幕可容纳多少行外星人"""
    available_space_y = (ai_settings.screen_height
                         - (3 * alien_height) - ship_height)
    number_rows = int(available_space_y / (2 * alien_height))
    return number_rows

def create_alien(ai_settings, screen, aliens, alien_number, row_number):
    """创建一个外星人并将其放在当前行"""
    alien = Alien(ai_settings, screen)
    alien_width = alien.rect.width
    alien.x = alien_width + 2 * alien_width * alien_number
    alien.rect.x = alien.x
    alien.rect.y = alien.rect.height + \
                   2 * alien.rect.height * row_number
    aliens.add(alien)

def create_fleet(ai_settings, screen, ship, aliens):
    """创建外星人群"""
    #创建一个外星人，并计算一行可容纳多少个外星人
    alien = Alien(ai_settings, screen)
    number_aliens_x = get_number_aliens_x(ai_settings,
                                          alien.rect.width)
    number_rows = get_number_rows(ai_settings, ship.rect.height,
                                  alien.rect.height)

    #创建第一行外星人
    for row_number in range(number_rows):
        for alien_number in range(number_aliens_x):
            create_alien(ai_settings, screen, aliens, alien_number,
                         row_number)

def check_fleet_edges(ai_settings, aliens):
    """有外星人到达边缘时采取相应的措施"""
    for alien in aliens.sprites():
        if alien.check_edges():
            change_fleet_direction(ai_settings, aliens)
            break

def change_fleet_direction(ai_settings, aliens):
    """将整群外星人下移，并改变它们的方向"""
    for alien in aliens.sprites():
        alien.rect.y += ai_settings.fleet_drop_speed
    ai_settings.fleet_direction *= -1

def ship_hit(ai_settings, stats, sb, screen, ship, aliens,
             bullets):
    """响应被外星人撞到的飞船"""
    #将 ships_left 减 1
    if stats.ships_left > 0:
        stats.ships_left -= 1

        #更新记分牌
        sb.prep_ships()

        #清空外星人列表和子弹列表
        aliens.empty()
        bullets.empty()

        #创建一群新的外星人，并将飞船放到屏幕底部中央
        create_fleet(ai_settings, screen, ship, aliens)
        ship.center_ship()

        #暂停
        sleep(1)

    else:
        stats.game_active = False
        pygame.mouse.set_visible(True)

def check_aliens_bottom(ai_settings, stats, sb, screen, ship,
                        aliens, bullets):
    """检查是否有外星人到达屏幕底端"""
    screen_rect = screen.get_rect()
    for alien in aliens.sprites():
        if alien.rect.bottom >= screen_rect.bottom:
            #像飞船被撞到一样进行处理
            ship_hit(ai_settings, stats, sb, screen, ship,
                     aliens, bullets)
            break

def update_aliens(ai_settings, stats, sb, screen, ship, aliens,
                  bullets):
    """更新外星人群中所以外星人的位置"""
    check_fleet_edges(ai_settings, aliens)
    aliens.update()

    #检测外星人和飞船之间的碰撞
    if pygame.sprite.spritecollideany(ship, aliens):
        ship_hit(ai_settings, stats, sb, screen, ship, aliens,
                 bullets)

    #检查是否有外星人到达屏幕底端
    check_aliens_bottom(ai_settings, stats, sb, screen, ship,
                        aliens, bullets)
    
```



**game_stats.py**

```python
class GameStats():
    """跟踪游戏的统计信息"""

    def __init__(self, ai_settings):
        """初始化统计信息"""
        self.ai_settings = ai_settings
        self.reset_stats()

        #游戏刚启动时处于活动状态
        self.game_active = False

        #在任何情况下都不应重置最高得分
        self.high_score = 0

    def reset_stats(self):
        """初始化在游戏运行期间可能变化的统计信息"""
        self.ships_left = self.ai_settings.ship_limit
        self.score = 0
        self.level = 1
        
```



**scoreboard.py**

```python
import pygame.font
from pygame.sprite import Group

from ship import Ship

class Scoreboard():
    """显示得分信息的类"""

    def __init__(self, ai_settings, screen, stats):
        """初始化显示得分涉及的属性"""
        self.screen = screen
        self.screen_rect = screen.get_rect()
        self.ai_settings = ai_settings
        self.stats = stats

        #显示得分信息时使用的字体设置
        self.text_color = (30, 30, 30)
        self.font = pygame.font.SysFont(None, 48)

        #准备包含得分的初始化图像
        self.prep_score()
        self.prep_high_score()
        self.prep_level()
        self.prep_ships()

    def prep_score(self):
        """将得分转化为一幅渲染的图像"""
        rounded_score = int(round(self.stats.score, -1))
        score_str = "{:,}".format(rounded_score)
        self.score_image = self.font.render(score_str, True,
                                            self.text_color,
                                            self.ai_settings.bg_color)

        #将得分放在屏幕右上角
        self.score_rect = self.score_image.get_rect()
        self.score_rect.right = self.screen_rect.right - 20
        self.score_rect.top = 20

    def prep_high_score(self):
        """将最高得分转化为渲染的图像"""
        high_score = int(round(self.stats.high_score, -1))
        high_score_str = "{:,}".format(high_score)
        self.high_score_image = self.font.render(high_score_str,
                                            True, self.text_color,
                                            self.ai_settings.bg_color)

        #将最高得分放在屏幕顶部中央
        self.high_score_rect = self.high_score_image.get_rect()
        self.high_score_rect.centerx = self.screen_rect.centerx
        self.high_score_rect.top = self.score_rect.top

    def prep_level(self):
        """将等级转化为渲染的图像"""
        self.level_image = self.font.render(str(self.stats.level),
                                            True,
                                            self.text_color,
                                            self.ai_settings.bg_color)

        #将等级放在得分下方
        self.level_rect = self.level_image.get_rect()
        self.level_rect.right = self.score_rect.right
        self.level_rect.top = self.score_rect.bottom + 10

    def prep_ships(self):
        """显示还余下多少搜飞船"""
        self.ships = Group()
        for ship_number in range(self.stats.ships_left):
            ship = Ship(self.ai_settings, self.screen)
            ship.rect.x = 10 + ship_number * ship.rect.width
            ship.rect.y = 10
            self.ships.add(ship)

    def show_score(self):
        """在屏幕上显示得分和最高得分"""
        self.screen.blit(self.score_image, self.score_rect)
        self.screen.blit(self.high_score_image,
                         self.high_score_rect)
        self.screen.blit(self.level_image, self.level_rect)

        #绘制飞船
        self.ships.draw(self.screen)
        
```



**settings.py**

```python
class Settings():
    """存储游戏的所有设置"""

    def __init__(self):
        """初始化游戏的设置"""
        # 屏幕设置
        self.screen_width = 1200
        self.screen_height = 800
        self.bg_color = (230, 230, 230)

        #飞船的设置
        self.ship_limit = 3

        #子弹设置
        self.bullet_width = 3
        self.bullet_height = 15
        self.bullet_color = 60, 60, 60
        self.bullets_allowed = 5

        #外星人设置
        self.fleet_drop_speed = 3

        #加快游戏节奏的速度
        self.speedup_scale = 1.1
        #外星人点数的提高速度
        self.score_scale = 1.5

        self.initialize_dynamic_settings()

    def initialize_dynamic_settings(self):
        """初始化随游戏进行而变化的设置"""
        self.ship_speed_factor = 1.5
        self.bullet_speed_factor = 3
        self.alien_speed_factor = 1

        #fleet_direction 为 1 表示向右；为 -1 表示向左
        self.fleet_direction = 1

        #计分
        self.alien_points = 50

    def increase_speed(self):
        """提高速度设置和外星人分值"""
        self.ship_speed_factor *= self.speedup_scale
        self.bullet_speed_factor *= self.speedup_scale
        self.alien_speed_factor *= self.speedup_scale

        self.alien_points = int(self.alien_points *
                                self.score_scale)
        print(self.alien_points)
        
```



**ship.py**

```python
import pygame
from pygame.sprite import Sprite

class Ship(Sprite):

    def __init__(self, ai_settings, screen):
        """初始化飞船并设置其初始位置"""
        super(Ship, self).__init__()
        self.screen = screen
        self.ai_settings = ai_settings

        #加载飞船图像并获取其外接矩形
        self.image = pygame.image.load('images/ship.bmp')
        self.rect = self.image.get_rect()
        self.screen_rect = screen.get_rect()

        #将每艘新飞船放在屏幕底部中央
        self.rect.centerx = self.screen_rect.centerx
        self.rect.bottom = self.screen_rect.bottom

        #在飞船的属性 center 中存储小数值
        self.center = float(self.rect.centerx)

        #移动标志
        self.moving_right = False
        self.moving_left = False

    def update(self):
        """根据移动标志调整飞船的位置"""
        if self.moving_right and self.rect.right < self.screen_rect.right:
            self.center += self.ai_settings.ship_speed_factor
        if self.moving_left and self.rect.left > 0:
            self.center -= self.ai_settings.ship_speed_factor

        #根据 self.center 更新 rect 对象
        self.rect.centerx = self.center

    def blitme(self):
        """在指定位置绘制飞船"""
        self.screen.blit(self.image, self.rect)

    def center_ship(self):
        """让飞船在屏幕上居中"""
        self.center = self.screen_rect.centerx
        
```

