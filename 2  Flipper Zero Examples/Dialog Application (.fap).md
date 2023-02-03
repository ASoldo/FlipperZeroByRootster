### Anatomy of Flipper Zero Application

dialogue
├── application.fam
├── dialogue_icon_10px.png
├── dialogue.c
└── README.md

Now let's see content of `application.fam`[^4]
```c
App(appid = "dialogue",
name = "Dialogue",
apptype = FlipperAppType.EXTERNAL,
entry_point = "about_settings_app",
cdefines = ["APP_DIALOGUE"],
requires =
[
"gui",
"dialogs",
],
stack_size = 1 * 1024,
order = 1000,
fap_icon = "hello_world_10px.png",
fap_category = "RootsterApps", )
```

Let's see stripped down version of Dialog app with just 2 screens.

```c
#include <furi.h>
#include <dialogs/dialogs.h>
#include <gui/gui.h>
#include <gui/view_dispatcher.h>
#include <gui/modules/empty_screen.h>
#include <notification/notification_messages.h>

typedef DialogMessageButton (*AboutDialogScreen)(DialogsApp* dialogs, DialogMessage* message);

static DialogMessageButton product_screen(DialogsApp* dialogs, DialogMessage* message) {
    DialogMessageButton result;

    char* screen_header = "Welcome User\n"
                          "This is header \n";
    const char* screen_text = "Mui importante\n"
                              "Ayyyy Maria";

    dialog_message_set_header(message, screen_header, 0, 0, AlignLeft, AlignTop);
    dialog_message_set_text(message, screen_text, 0, 26, AlignLeft, AlignTop);
    result = dialog_message_show(dialogs, message);
    dialog_message_set_header(message, NULL, 0, 0, AlignLeft, AlignTop);
    dialog_message_set_text(message, NULL, 0, 0, AlignLeft, AlignTop);

    return result;
}

static DialogMessageButton compliance_screen(DialogsApp* dialogs, DialogMessage* message) {
    DialogMessageButton result;

    const char* screen_text = "New screen\n"
                              "2nd row for text\n"
                              "this is place for more text";

    dialog_message_set_text(message, screen_text, 0, 0, AlignLeft, AlignTop);
    result = dialog_message_show(dialogs, message);
    dialog_message_set_text(message, NULL, 0, 0, AlignLeft, AlignTop);

    return result;
}

const AboutDialogScreen about_screens[] = {
    product_screen,
    compliance_screen,
};

const size_t about_screens_count = sizeof(about_screens) / sizeof(AboutDialogScreen);

int32_t about_settings_app(void* p) {
    UNUSED(p);
    DialogsApp* dialogs = furi_record_open(RECORD_DIALOGS);
    DialogMessage* message = dialog_message_alloc();

    Gui* gui = furi_record_open(RECORD_GUI);
    ViewDispatcher* view_dispatcher = view_dispatcher_alloc();
    EmptyScreen* empty_screen = empty_screen_alloc();
    const uint32_t empty_screen_index = 0;

    size_t screen_index = 0;
    DialogMessageButton screen_result;

    // draw empty screen to prevent menu flickering
    view_dispatcher_add_view(
        view_dispatcher, empty_screen_index, empty_screen_get_view(empty_screen));
    view_dispatcher_attach_to_gui(view_dispatcher, gui, ViewDispatcherTypeFullscreen);
    view_dispatcher_switch_to_view(view_dispatcher, empty_screen_index);

    while(1) {
        if(screen_index >= about_screens_count - 1) {
            dialog_message_set_buttons(message, "Back", NULL, NULL);
        } else {
            dialog_message_set_buttons(message, "Back", NULL, "Next");
        }

        screen_result = about_screens[screen_index](dialogs, message);

        if(screen_result == DialogMessageButtonLeft) {
            if(screen_index <= 0) {
                break;
            } else {
                screen_index--;
            }
        } else if(screen_result == DialogMessageButtonRight) {
            if(screen_index < about_screens_count) {
                screen_index++;
            }
        } else if(screen_result == DialogMessageButtonBack) {
            break;
        }
    }

    dialog_message_free(message);
    furi_record_close(RECORD_DIALOGS);

    view_dispatcher_remove_view(view_dispatcher, empty_screen_index);
    view_dispatcher_free(view_dispatcher);
    empty_screen_free(empty_screen);
    furi_record_close(RECORD_GUI);

    return 0;
}
```