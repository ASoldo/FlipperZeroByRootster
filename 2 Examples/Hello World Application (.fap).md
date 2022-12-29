In this section we will take a look into how flipper zero handles applications and what are they exactly.

Flipper Zero Applications consists of 2 parts, "System Applications"[^1] and "Plugins"[^2].

When we want to make our own application, we need to place it in special folder that Flipper Zero Developers give us access to, and that is `applications_user`[^3] folder.
Here we will create some basic folder structure with some necesery files in it.

Anatomy of Flipper Zero Application

hello_world
├── application.fam [^4]
├── hello_world_10px.png
├── hello_world.c[^5]
└── README.md

Now let's see content of `application.fam`[^4]
```c
App(
    appid="hello_world",
    name="Hello World",
    apptype=FlipperAppType.EXTERNAL,
    entry_point="hello_world_app",
    cdefines=["APP_HELLO_WORLD"],
    requires=["gui"],
    stack_size=1 * 1024,
    order=30,
    fap_icon="hello_world_10px.png",
    fap_category="RootsterApps",
)
```

As we can see from this code example there are some basic informations that our app needs in order to be valid `.fap` application. Most of the properties are self explanatory but there are some differences that you need to know. 
1. `apptype=FlipperAppType.EXTERNAL` is required to be marked as `Plugin`[^2]
2. `entry_point=hello_world_app` with prefix `_app`
3. `cdefines=[APP_HELLO_WORLD]` is required
4. `requires=["gui"]` is required when we our app print something on screen otherwise this can be empty string [""]

For start is best to copy the whole script and only change relevant part if your app will be called anything else but "Hello World":
1. appid
2. name
3. entry_point
4. cdefines
5. fap_icon
6. fap_category

Now let's look how hello_world.c looks like.

```c
// https://github.com/DroomOne/Flipper-Plugin-Tutorial/blob/main/hello_world.c moving text on input
// https://github.com/zmactep/flipperzero-hello-world/blob/main/3_gui/hello_world.c original
// lab.flipper.net/cli log command

// instructions
// ./fbt faps -> build all
// ./fbt launch_app APPSRC=hello_world -> run app on device

#include <stdio.h>
#include <furi.h>
#include <gui/gui.h>
#include <input/input.h>
#include <notification/notification_messages.h>


static void draw_callback(Canvas* canvas, void* ctx) {
    UNUSED(ctx);

    canvas_clear(canvas);
    canvas_set_font(canvas, FontPrimary);

    // canvas_draw_line(canvas, 0, 15, 50, 50);
    // canvas_draw_triangle(canvas, 50, 50, 10, 10, CanvasDirectionBottomToTop);
    // canvas_draw_box(canvas, 75, 50, 35, 35);
    // canvas_draw_box(canvas, 0, 0, 80, 10);
    // canvas_draw_circle(canvas, 75, 25, 8);

    canvas_draw_str(canvas, 64, 32, "hello :)");
}

static void input_callback(InputEvent* input_event, void* ctx) {
    // Checking if the context is not null
    furi_assert(ctx);
    FuriMessageQueue* event_queue = ctx;
    furi_message_queue_put(event_queue, input_event, FuriWaitForever);
}

int32_t hello_world_app(void* p) {
    UNUSED(p);
    extern const NotificationSequence sequence_blink_red_100;
    // Current event of type InputEvent
    InputEvent event;
    // Event queue for 8 elements of size InputEvent
    FuriMessageQueue* event_queue = furi_message_queue_alloc(8, sizeof(InputEvent));

    // Create a new viewport
    ViewPort* view_port = view_port_alloc();
    // Create a render callback, without context
    view_port_draw_callback_set(view_port, draw_callback, NULL);
    // Create a callback for keystrokes, pass as context
    // our message queue to push these events into it
    view_port_input_callback_set(view_port, input_callback, event_queue);

    // Creating a GUI Application
    Gui* gui = furi_record_open(RECORD_GUI);
    // Connect view port to GUI in full screen mode
    gui_add_view_port(gui, view_port, GuiLayerFullscreen);
    // Used to notify the user by blinking red (error) or blue (fetch successful)
    NotificationApp* notifications = furi_record_open(RECORD_NOTIFICATION);

    // Infinite event queue processing loop
    while(1) {
        // Select an event from the queue into the event variable (wait indefinitely if the queue is empty)
        // and check that we managed to do it
        furi_check(furi_message_queue_get(event_queue, &event, FuriWaitForever) == FuriStatusOk);

        if((event.type == InputTypePress) || (event.type == InputTypeRepeat)) {
            // If the "back" button is pressed, then we exit the loop, and hence the application
            if(event.key == InputKeyBack) {
                break;
            }
            if(event.key == InputKeyRight) {
                FURI_LOG_D("integer %i", someintvalue);
                // FURI_LOG_D("character %c", charvalue);
                // FURI_LOG_D("integer %s", chararray);
                // FURI_LOG_D("integer %i %c", someintvalue, charvarlue);
            }
            if(event.key == InputKeyLeft) {
                FURI_LOG_D("my app", "Rootster %i", 2);

                // FURI_LOG_D("integer %i", someintvalue);
                // FURI_LOG_D("character %c", charvalue);
                // FURI_LOG_D("integer %s", chararray);
                // FURI_LOG_D("integer %i %c", someintvalue, charvarlue);
            }
            if(event.key == InputKeyUp) {
                FURI_LOG_D("integer %i", someintvalue);
                // FURI_LOG_D("character %c", charvalue);
                // FURI_LOG_D("integer %s", chararray);
                // FURI_LOG_D("integer %i %c", someintvalue, charvarlue);
            }
            if(event.key == InputKeyDown) {
                FURI_LOG_D("my app", "Rootster %i", 4);

                // FURI_LOG_D("integer %i", someintvalue);
                // FURI_LOG_D("character %c", charvalue);
                // FURI_LOG_D("integer %s", chararray);
                // FURI_LOG_D("integer %i %c", someintvalue, charvarlue);
            }
            if(event.key == InputKeyOk) {
                FURI_LOG_D("my app", "Rootster %i", 4);
                notification_message(notifications, &sequence_blink_red_100);

                // FURI_LOG_D("integer %i", someintvalue);
                // FURI_LOG_D("character %c", charvalue);
                // FURI_LOG_D("integer %s", chararray);
                // FURI_LOG_D("integer %i %c", someintvalue, charvarlue);
            }
        }
        view_port_update(view_port);
        // https://lab.flipper.net/cli
        // My Flipper
        // Web platform for your Flipper
        // choose cli, then type this command:
        // log
    }

    // Special cleanup of memory occupied by the queue
    furi_message_queue_free(event_queue);
    view_port_enabled_set(view_port, false);
    // We clean the created objects associated with the interface
    gui_remove_view_port(gui, view_port);
    view_port_free(view_port);
    furi_record_close(RECORD_GUI);

    return 0;
}
```

Copy and paste this whole script and paste it into your hello_world.c.
This code will:
1. Print `Hello :)` on screen
2. When using arrows,back key, ok key message will be printed in the console
3. Back button press will exit application

This code include some of important explanations what line does what and it's good resource for extra info about API and how it is used.

### ToDos vamanos:
- [ ] Explain more about code above.

[^1]: System applications are all applications that are used by Flipper Zero internaly
[^2]: Plugins are applications that can be developed by 3rd party developer
[^3]: This folder is included in `.gitignore` file that prevents checking and syncing with this folder and all contents of it. That means, we can pull official git updates on `dev` branch and still have our own git project inside of `applications_user`
[^4]: This is base setup for our application
[^5]: This is the meat of our application