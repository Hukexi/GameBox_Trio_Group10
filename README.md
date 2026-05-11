# GameBox Trio — Group 10

XJEL2645 Embedded Systems Project | Unit 4 Group Project  
STM32 Nucleo-L476RG · 1.54" IPS LCD (240×240) · Joystick · Buzzer

---

## Games

| # | Game | Description |
|---|------|-------------|
| 1 | **Road Trip** | Racing game with obstacles, nitro boost, 7-segment countdown and background music |
| 2 | **Mario** | Platformer with 5-LED health bar, coin flash, haptic feedback and audio |
| 3 | **Pong+** | Breakout-style game with power-ups, explosions and multi-ball |

---

## Project Structure

```
GameBox_Trio_Group10/
├── Core/                        # STM32 HAL auto-generated files
├── Drivers/                     # STM32 HAL drivers
├── shared/                      # Shared by all games
│   ├── Menu.h / Menu.c          # FSM carousel menu
│   └── InputHandler.h / .c      # Joystick + button debounce
├── game_1/                      # Road Trip
│   ├── Game_1.c
│   ├── RacingEngine/
│   ├── Road/  Player/  Obstacle/
│   ├── BGMusic.c
│   ├── SevenSeg.c
│   ├── NitroSensor.c
│   └── Sprites/
├── game_2/                      # Mario
│   ├── Game_2.c
│   ├── MarioGame.c  MarioLevel.c
│   ├── MarioRender.c  MarioInput.c
│   ├── MarioAudio.c  MarioUI.c
│   └── MarioHaptics.c  MarioHealthLeds.c
├── game_3/                      # Pong+
│   └── Game_3.c
├── ST7789V2_Driver_STM32L4/     # LCD SPI + DMA driver
├── Joystick/                    # ADC joystick driver
├── Buzzer/                      # TIM2 PWM buzzer driver
└── PWM/
```

---

## Architecture

A central FSM in `main.c` dispatches between the menu and each game. Each game runs its own frame loop and returns `MENU_STATE_HOME` on exit.

```c
while (1) {
    switch (current_state) {
        case MENU_STATE_HOME:   current_state = Menu_Run(&menu);  break;
        case MENU_STATE_GAME_1: current_state = Game1_Run();      break;
        case MENU_STATE_GAME_2: current_state = Game2_Run();      break;
        case MENU_STATE_GAME_3: current_state = Game3_Run();      break;
    }
}
```

Shared infrastructure (LCD, InputHandler, Buzzer, Timers) is initialised once at boot and accessed by all games.

---

## Hardware Pin Reference

| Peripheral | Pin(s) | Interface |
|------------|--------|-----------|
| LCD ST7789V2 240×240 | PB15, PB13, PB12, PB11, PB2, PB1 | SPI2 + DMA1 CH5 |
| Joystick X / Y | PA4, PC1 | ADC1 |
| Joystick button | PC3 | GPIO EXTI |
| Buzzer SFX | PB10 | TIM2 CH3 PWM |
| Buzzer BGM (Game 1) | PA1 | TIM2 CH2 PWM |
| Buttons | PC2, PC3 | GPIO EXTI |
| Speed / Health LEDs | PB6, PB8, PB9, PA9, PC6 | GPIO output |
| Coin flash LED (Game 2) | PB5 | GPIO output |
| 7-segment display (Game 1) | PC7–PC12, PD2 | GPIO output |

---

## Controls

| Input | Action |
|-------|--------|
| Joystick UP / DOWN | Navigate menu / in-game movement |
| BTN2 (PC2) | Game action / back |
| BTN3 (PC3) | Confirm / select |

---

## Build & Flash

1. Open project in **STM32CubeIDE**
2. **Project → Build** (or Ctrl + B)
3. **Run → Run + Debug** to flash to the Nucleo board

---

## License

Academic project — University of Leeds, School of Electronic & Electrical Engineering, 2025/26.
