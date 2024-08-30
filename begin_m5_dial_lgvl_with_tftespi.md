### Review Memo: Getting Started with LGVL and M5 Dial on TFT ESP

#### Objective:
This memo provides step-by-step instructions on how to begin working with the Lightweight Generic Vector Library (LGVL) and an M5 Dial over a TFT display using ESP32.

#### Requirements:
- **Hardware:**
  - M5 Dial (rotary encoder) for input control
 
  
- **Software:**
  - Arduino IDE
  - TFT_eSPI library (for display management)
  - LVGL library (for GUI management)
  - M5Stack library (for handling the M5 Dial)

#### Step-by-Step Guide:

1. **Setup the Development Environment:**
   - **Arduino IDE:**
     - Ensure you have the latest version of Arduino IDE installed.
     - Install the **ESP32** board support via the Arduino Board Manager.
     - Install the **TFT_eSPI** and **LVGL** libraries from the Arduino Library Manager.
     - Install the **M5Stack** library if you're using an M5Stack device.

2. **Configure TFT_eSPI:**
   - Navigate to the `TFT_eSPI` library folder and open the `User_Setup.h` file.
   - Modify the settings according to your specific TFT display. This includes defining the correct display driver, pins, and resolution.
   ```cpp
   // See SetupX_Template.h for all options available
    #define USER_SETUP_ID 303

    #define GC9A01_DRIVER

    #define TFT_MISO -1
    #define TFT_MOSI 5 
    #define TFT_SCLK 6
    #define TFT_CS 7 
    #define TFT_DC 4 
    #define TFT_RST 8 
    #define TFT_BL 9 
    #define TFT_BACKLIGHT_ON HIGH

    #define LOAD_GLCD   // Font 1. Original Adafruit 8 pixel font needs ~1820 bytes in FLASH
    #define LOAD_FONT2  // Font 2. Small 16 pixel high font, needs ~3534 bytes in FLASH, 96 characters
    #define LOAD_FONT4  // Font 4. Medium 26 pixel high font, needs ~5848 bytes in FLASH, 96 characters
    #define LOAD_FONT6  // Font 6. Large 48 pixel font, needs ~2666 bytes in FLASH, only characters 1234567890:-.apm
    #define LOAD_FONT7  // Font 7. 7 segment 48 pixel font, needs ~2438 bytes in FLASH, only characters 1234567890:.
    #define LOAD_FONT8  // Font 8. Large 75 pixel font needs ~3256 bytes in FLASH, only characters 1234567890:-.
    #define LOAD_GFXFF  // FreeFonts. Include access to the 48 Adafruit_GFX free fonts FF1 to FF48 and custom fonts
    #define SMOOTH_FONT

    #define TFT_WIDTH 240
    #define TFT_HEIGHT 240

    #define SPI_FREQUENCY  80000000

    #define USE_HSPI_PORT  
    //#define SUPPORT_TRANSACTIONS
   ```

3. **Initialize LVGL config:**
   - Copy the `lv_conf_template.h` file from the LVGL library to libraries folder. rename it to `lv_conf.h`.
   - Modify the `lv_conf.h` file to configure the LVGL library according to your requirements. enable the tft_espi display driver 
        ```cpp
            /*Interface for TFT_eSPI*/
            #define LV_USE_TFT_ESPI         1
        ```
4. **Change LVGL rotation:**
   - Open the `lvgl\src\drivers\display\tft_espi\lv_tft_espi.cpp` file. Change the line 
        ```cpp
            dsc->tft->setRotation(4);   /* Landscape orientation, flipped */
        ```
   - Change the rotation value according to your display orientation.
        ``` cpp
        #if LV_USE_TFT_ESPI
        /*TFT_eSPI can be enabled lv_conf.h to initialize the display in a simple way*/
        disp = lv_tft_espi_create(TFT_HOR_RES, TFT_VER_RES, draw_buf, sizeof(draw_buf));
        lv_display_set_rotation(disp,LV_DISPLAY_ROTATION_0);
        ```
5. **Full example code**
```cpp
/*
Modify for use with M5 Dial via TFT_eSPI
Using LVGL with Arduino requires some extra steps:
 *Be sure to read the docs here: https://docs.lvgl.io/master/get-started/platforms/arduino.html  */
#include <ft3267.h>
#include <Wire.h>
#define TP_SDA 11
#define TP_SCL 12
#define SDA_PIN TP_SDA
#define SCL_PIN TP_SCL
#define TOUCH_INT 14
#include <Encoder.h>
#define EncoderA 41
#define EncoderB 40
#define EncoderBtn 42
#include <lvgl.h>
Encoder encoder(EncoderA, EncoderB);
#if LV_USE_TFT_ESPI
#include <TFT_eSPI.h>
#endif

/*To use the built-in examples and demos of LVGL uncomment the includes below respectively.
 *You also need to copy `lvgl/examples` to `lvgl/src/examples`. Similarly for the demos `lvgl/demos` to `lvgl/src/demos`.
 *Note that the `lv_examples` library is for LVGL v7 and you shouldn't install it for this version (since LVGL v8)
 *as the examples and demos are now part of the main LVGL library. */

#include <examples/lv_examples.h>
#include <demos/lv_demos.h>

/*Set to your screen resolution*/
#define TFT_HOR_RES 240
#define TFT_VER_RES 240

/*LVGL draw into this buffer, 1/10 screen size usually works well. The size is in bytes*/
#define DRAW_BUF_SIZE (TFT_HOR_RES * TFT_VER_RES / 10 * (LV_COLOR_DEPTH / 8))
uint32_t draw_buf[DRAW_BUF_SIZE / 4];

#if LV_USE_LOG != 0
void my_print(lv_log_level_t level, const char *buf) {
  LV_UNUSED(level);
  Serial.println(buf);
  Serial.flush();
}
#endif

/* LVGL calls it when a rendered image needs to copied to the display*/
void my_disp_flush(lv_display_t *disp, const lv_area_t *area, uint8_t *px_map) {
  /*Copy `px map` to the `area`*/

  /*For example ("my_..." functions needs to be implemented by you)
    uint32_t w = lv_area_get_width(area);
    uint32_t h = lv_area_get_height(area);

    my_set_window(area->x1, area->y1, w, h);
    my_draw_bitmaps(px_map, w * h);
     */

  // uint32_t w = (area->x2 - area->x1 + 1);
  // uint32_t h = (area->y2 - area->y1 + 1);

  // tft.startWrite();
  // tft.setAddrWindow(area->x1, area->y1, w, h);
  // tft.pushColors(&color_p->full, w * h, true);
  // tft.endWrite();
  lv_disp_flush_ready(disp);
}

/*Read the touchpad*/
void my_touchpad_read(lv_indev_t *indev, lv_indev_data_t *data) {
  /*For example  ("my_..." functions needs to be implemented by you)
    int32_t x, y;
    bool touched = my_get_touch( &x, &y );

    if(!touched) {
        data->state = LV_INDEV_STATE_RELEASED;
    } else {
        data->state = LV_INDEV_STATE_PRESSED;

        data->point.x = x;
        data->point.y = y;
    }
     */
  uint8_t touch_points_num;
  uint16_t x, y;
  esp_err_t ret = ft3267_read_pos(&touch_points_num, &x, &y);
  if (ret == ESP_OK && touch_points_num > 0) {
    data->state = LV_INDEV_STATE_PRESSED;
    data->point.x = x;
    data->point.y = y;
  } else {
    data->state = LV_INDEV_STATE_RELEASED;
  }
}
static void encoder_read(lv_indev_t *indev_drv, lv_indev_data_t *data) {
  static long pos = 0;
  long newPos = encoder.read() / 4;
  if (newPos != pos) {
    data->enc_diff = newPos - pos;
    Serial.println(pos);
    pos = newPos;
  }
  // data->enc_diff = encoder.getCount(true);
  // data->state = M5.BtnA.isPressed() ? LV_INDEV_STATE_PR : LV_INDEV_STATE_REL;
}

void setup() {
  String LVGL_Arduino = "Hello Arduino! ";
  LVGL_Arduino += String('V') + lv_version_major() + "." + lv_version_minor() + "." + lv_version_patch();

  Serial.begin(115200);
  Serial.println(LVGL_Arduino);
  Wire.begin(SDA_PIN, SCL_PIN);
  // Initialize the FT3267 touch panel
  esp_err_t ret = ft3267_init(Wire);
  lv_init();

  /*Set a tick source so that LVGL will know how much time elapsed. */
  lv_tick_set_cb(millis);

  /* register print function for debugging */
#if LV_USE_LOG != 0
  lv_log_register_print_cb(my_print);
#endif

  lv_display_t *disp;
#if LV_USE_TFT_ESPI
  /*TFT_eSPI can be enabled lv_conf.h to initialize the display in a simple way*/
  disp = lv_tft_espi_create(TFT_HOR_RES, TFT_VER_RES, draw_buf, sizeof(draw_buf));
  lv_display_set_rotation(disp,LV_DISPLAY_ROTATION_0);
#else
  /*Else create a display yourself*/
  disp = lv_display_create(TFT_HOR_RES, TFT_VER_RES);
  lv_display_set_flush_cb(disp, my_disp_flush);
  lv_display_set_buffers(disp, draw_buf, NULL, sizeof(draw_buf), LV_DISPLAY_RENDER_MODE_PARTIAL);
#endif

  /*Initialize the (dummy) input device driver*/
  lv_indev_t *indev = lv_indev_create();
  lv_indev_set_type(indev, LV_INDEV_TYPE_POINTER); /*Touchpad should have POINTER type*/
  lv_indev_set_read_cb(indev, my_touchpad_read);
  lv_indev_t *indev_encoder;
  indev_encoder = lv_indev_create();
  lv_indev_set_type(indev_encoder, LV_INDEV_TYPE_ENCODER);
  lv_indev_set_read_cb(indev_encoder, encoder_read);

  /* Create a simple label
     * ---------------------
     lv_obj_t *label = lv_label_create( lv_scr_act() );
     lv_label_set_text( label, "Hello Arduino, I'm LVGL!" );
     lv_obj_align( label, LV_ALIGN_CENTER, 0, 0 );

     * Try an example. See all the examples
     *  - Online: https://docs.lvgl.io/master/examples.html
     *  - Source codes: https://github.com/lvgl/lvgl/tree/master/examples
     * ----------------------------------------------------------------

    //  lv_example_btn_1();

     * Or try out a demo. Don't forget to enable the demos in lv_conf.h. E.g. LV_USE_DEMOS_WIDGETS
     * -------------------------------------------------------------------------------------------

     lv_demo_widgets();
     */
  // lv_obj_t *label = lv_label_create( lv_scr_act() );
  // lv_label_set_text( label, "Hello Arduino, I'm LVGL!" );
  // lv_obj_align( label, LV_ALIGN_CENTER, 0, -30 );
  // lv_example_event_1();
  //lv_example_get_started_3();
  lv_example_event_3();
  Serial.println("Setup done");
}

void loop() {
  lv_task_handler(); /* let the GUI do its work */
  delay(5);          /* let this time pass */
}


```

