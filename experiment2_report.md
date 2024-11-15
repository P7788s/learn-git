# 实验二 外星人入侵游戏开发

班级： 22物联2

学号： B20220305232

姓名： 师钰茹

Gitee地址：<https://gitee.com/aqqaqq/alien_invasion.git>

---

## 实验目的

1. Python基础：Python基础语法、Python标准库
2. Pygame的使用
3. 使用Git进行版本控制和团队协作

## 实验环境

1. Git
2. VSCode
3. VSCode插件

## 实验项目分工协作情况
  大概是根据代码量来分工
### 项目组员
刘宇欣、师钰茹、俞懿梅、何宛彤、丰嘉萱
### 项目分工
 - 师钰茹：负责 alien_invasion 文件中的 1 - 139 行
 - 俞懿梅：负责 alien_invasion 文件中的 140 - 265 行
 - 何宛彤：负责 alien、bullet 和 button 文件
 - 刘宇欣：负责 game_stats 和 scoreboard 文件
 - 丰嘉萱：负责 settings 和 ship 文件
## 实验过程记录
  我负责alien_invasion文件的前半部分即1-139行，主要部分是游戏的初始化，并创建游戏资源；游戏的主循环；响应按键事件：鼠标键盘按键事件和游戏中“Play”按钮的事件。
同时还包括添加该项目中使用到的python标准库模块和用户自定义模块。代码部分如下
#### 第一步：添加本游戏项目中使用到的Python标准库和用户自定义库
```Python
import sys
from time import sleep
import pygame
import os

from settings import Settings
from game_stats import GameStats
from scoreboard import Scoreboard
from button import Button
from ship import Ship
from bullet import Bullet
from alien import Alien
```
#### 第二步：创建一个用于整个游戏的管理游戏资源和行为的类，包括游戏界面的背景和游戏中的角色的初始化，还有游戏的行为如游戏开始按钮，计分，记录最高分，记录玩家等级等。
```Python
class AlienInvasion:
    """管理游戏资源和行为的类"""

    def __init__(self):
        """初始化游戏并创建游戏资源"""
        pygame.init()
        self.clock = pygame.time.Clock()
        self.settings = Settings()

        self.screen = pygame.display.set_mode(
            (self.settings.screen_width, self.settings.screen_height))
        os.environ['SDL_VIDEO_CENTERED'] = '1' #居中显示
        self.screen = pygame.display.set_mode((1000, 600))
        pygame.display.set_caption("Alien Invasion")

        # 创建存储游戏统计信息的实例，并创建记分牌
        self.stats = GameStats(self)
        self.sb = Scoreboard(self)

        self.ship = Ship(self)
        self.bullets = pygame.sprite.Group()
        self.aliens = pygame.sprite.Group()

        self._create_fleet()

        # 设置背景色
        self.bg_color = (230, 230, 230)

        # 外星人设置
        self.alien_speed = 1.0

        # 让游戏在一开始处于非活动状态
        self.game_active = False

        # 创建 Play 按钮
        self.play_button = Button(self, "Play")
```
#### 第三步：编写游戏的主循环，主要包括游戏中角色和屏幕的刷新，及check事件执行。
```Python
    def run_game(self):
        """开始游戏的主循环"""
        while True:
            self._check_events()

            if self.game_active:
                self.ship.update()
                self._update_bullets()
                self._update_aliens()
                
            self._update_screen()
            self.clock.tick(60)
```
#### 第四步：编写按键和鼠标的响应事件，通过引用pygame库中的函数(event.get())、类(sprite)、常量(pygame.K_RIGHT按键常量)等。根据按键行为写对应的事件。
```Python
    def _check_events(self):
        """响应按键和鼠标事件"""
        for event in pygame.event.get():
            if event.type == pygame.QUIT:
                sys.exit()
            elif event.type == pygame.KEYDOWN:
                self._check_keydown_events(event)
            elif event.type == pygame.KEYUP:
                self._check_keyup_events(event)
            elif event.type == pygame.MOUSEBUTTONDOWN:
                mouse_pos = pygame.mouse.get_pos()
                self._check_play_button(mouse_pos)

  def _check_keydown_events(self, event):
        """响应按下"""
        if event.key == pygame.K_RIGHT:
            self.ship.moving_right = True
        elif event.key == pygame.K_LEFT:
            self.ship.moving_left = True
        elif event.key == pygame.K_q:
            sys.exit()
        elif event.key == pygame.K_SPACE:
            self._fire_bullet()   

    def _check_keyup_events(self, event):
        """响应释放"""
        if event.key == pygame.K_RIGHT:
            self.ship.moving_right = False
        elif event.key == pygame.K_LEFT:
            self.ship.moving_left = False

```
#### 第五步：编写游戏中的按键响应事件，例如点击Play按钮，将开始游戏，并且游戏内容开始运行例如重置游戏统计信息，创建新的游戏角色等。
```Python
    def _check_play_button(self, mouse_pos):
        """在玩家单击 Play 按钮时开始新游戏"""
        button_clicked = self.play_button.rect.collidepoint(mouse_pos)
        if button_clicked and not self.game_active:
            # 还原游戏设置
            self.settings.initialize_dynamic_settings()

            # 重置游戏的统计信息
            self.stats.reset_stats()
            self.sb.prep_score()
            self.sb.prep_level()
            self.sb.prep_ships()
            self.game_active = True

            # 清空外星人列表和子弹列表
            self.bullets.empty()
            self.aliens.empty()
            
            # 创建一个新的外星舰队，并将飞船放在屏幕底部的中央
            self._create_fleet()
            self.ship.center_ship()

            # 隐藏光标
            pygame.mouse.set_visible(False)
````
#### 第六步：编写游戏的其中一个角色“子弹”，并定义子弹初始化的函数和子弹更新的函数，并且在其中对消失的子弹计数以达成记录分数的效果。
```Python
    def _fire_bullet(self):
        """创建一颗子弹，并将其加入编组 bullets """
        if len(self.bullets) < self.settings.bullets_allowed:
            new_bullet = Bullet(self)
            self.bullets.add(new_bullet)

    def _update_bullets(self):
        """更新子弹的位置并删除已消失的子弹"""
        # 更新子弹的位置
        self.bullets.update()

        # 删除已消失的子弹
        for bullet in self.bullets.copy():
            if bullet.rect.bottom <= 0:
                self.bullets.remove(bullet)
        print(len(self.bullets))

        self._check_bullet_alien_collisions()
```


## 实验总结
  以上是我的负责部分。通过此次的项目操作
  深入学习了 Python 中 Pygame 库的使用。它为游戏开发提供了全面的功能支持，从游戏窗口的创建与显示设置（如使用pygame.display.set_mode()来初始化游戏窗口），到用户输入事件的处理（包括键盘和鼠标事件，像pygame.event.get()用于获取事件，pygame.KEYDOWN、pygame.KEYUP和pygame.MOUSEBUTTONDOWN等常量用于区分不同类型的用户操作），再到游戏元素（如飞船、子弹、外星人等）的绘制与更新。这使我认识到利用成熟的第三方库能够极大地提高开发效率，避免重复造轮子。
  再次使用 Gitee 上传本地代码到远程仓库，进一步加深了对代码版本控制的理解。这种方式对于小组合作开发项目尤其重要，它允许团队成员同时在不同分支上工作，有效地管理代码的不同版本和变更历史。同时，通过合并分支等操作，能轻松整合各个成员的工作成果，避免代码冲突或丢失修改。
程序语言语法：
  Python 函数定义（def）：大量运用了def关键字来定义函数，清晰地将游戏的不同功能模块封装在各自的函数中，如_check_events、_check_keydown_events、_check_keyup_events等。这体现了 Python 函数定义的简洁性和灵活性，每个函数接受特定的参数（如事件对象、键盘按键值等），执行相应的逻辑，并可以返回结果或直接影响游戏状态。
  条件语句（if - elif - else）：在处理各种游戏事件和逻辑判断中广泛使用了条件语句。例如，在_check_keydown_events函数中，通过if - elif结构判断按下的是哪个键盘按键，并执行相应的游戏操作，如移动飞船或发射子弹。这种语法结构使得代码能够根据不同的条件执行不同的分支，实现复杂的逻辑控制。
  循环语句（while、for）：在游戏主循环（while True）中实现了游戏的持续运行和更新。for循环则用于遍历事件队列（for event in pygame.event.get():）和游戏元素列表（如子弹列表for bullet in self.bullets.copy():），这种循环结构保证了对每个元素的操作都能按顺序执行，是实现游戏动态更新的关键。
数据结构：
  类（class）的运用：创建了多个自定义类，如Settings、GameStats、Scoreboard、Button、Ship、Bullet和Alien，每个类都有自己的属性和方法，用于封装游戏中不同对象的相关数据和行为。这体现了面向对象编程中类作为数据和方法的集合体的概念，通过实例化这些类的对象，可以方便地管理和操作游戏中的各种元素。
  精灵组（pygame.sprite.Group）：利用pygame.sprite.Group来管理游戏中的精灵对象（如子弹和外星人）。这种数据结构方便了对多个相关对象的批量操作，比如更新所有子弹的位置（self.bullets.update()）、检测子弹与外星人的碰撞（pygame.sprite.groupcollide(self.bullets, self.aliens, True, True)）等，提高了代码的可读性和效率。
编程技巧
游戏状态管理：通过设置游戏状态变量（如self.game_active）来控制游戏的不同阶段，如游戏开始前显示 “Play” 按钮、游戏进行中处理各种游戏元素的交互以及游戏结束后的相关操作。这种基于状态的编程技巧使得游戏逻辑更加清晰，易于维护和扩展。
事件驱动编程：基于 Pygame 的事件处理机制，实现了事件驱动的编程模式。游戏在主循环中不断监听各种事件（键盘、鼠标等），然后根据不同的事件类型执行相应的函数。这种模式使得游戏能够及时响应用户的操作，提高了游戏的交互性。
碰撞检测与处理：运用pygame.sprite模块提供的碰撞检测功能（如pygame.sprite.groupcollide和pygame.sprite.spritecollideany）来处理游戏元素之间的碰撞。在子弹与外星人、飞船与外星人的碰撞检测中，不仅实现了相应游戏元素的删除或其他操作（如得分计算、飞船受损处理），还保证了游戏逻辑的合理性和流畅性。
编程思想
模块化与封装思想：将游戏的不同功能模块分别封装在不同的函数和类中，使得每个部分都具有相对独立的功能，如Settings类负责游戏设置参数的管理，Scoreboard类专注于得分显示。这种模块化的设计降低了代码的耦合度，提高了代码的可维护性和可复用性，方便对单个模块进行修改和测试，而不会影响其他模块的功能。
面向对象编程思想：整个游戏的设计充分体现了面向对象编程（OOP）思想。各个游戏元素（飞船、子弹、外星人等）都被抽象成类，每个类都有自己的属性（如位置、速度等）和方法（如移动、绘制等）。这种设计方式符合现实世界中对象的概念，使得代码结构更加清晰，易于理解和扩展，同时也方便实现继承、多态等更高级的 OOP 特性（虽然在当前代码中可能未完全体现这些高级特性，但为进一步的功能扩展提供了良好的基础）。
综上所述，这次实验不仅让我熟练掌握了 Pygame 库在游戏开发中的应用，也在编程工具使用、Python 语言语法、数据结构、编程技巧和编程思想等多个方面得到了全面的锻炼和提升，为今后更复杂的项目开发奠定了坚实的基础。
