<script>
// Line drawing tool
// TODO: make an angle-snap when "Shift" is pressed

import Overlay from "../../mixins/overlay.js";
import Tool from "../../mixins/tool.js";
import Icons from "../../stuff/icons.json";
import Pin from "../primitives/pin.js";
import Seg from "../primitives/seg.js";
import Line from "../primitives/line.js";
import VLine from "../primitives/vline.js";
import Ray from "../primitives/ray.js";

export default {
  name: "VLineTool",
  mixins: [Overlay, Tool],
  methods: {
    meta_info() {
      return { author: "C451", version: "1.1.0" };
    },
    tool() {
      return {
        // Descriptor for the tool
        //group: "VLines",
        icon: Icons["vline.png"],
        type: "Extended",
        hint: "This hint will be shown on hover",
        data: [], // Default data
        settings: { extended: true }, // Default settings
        // Modifications
        // mods: {
        //   Extended: {
        //     // Rewrites the default setting fields
        //     settings: { extended: true },
        //     icon: Icons["extended.png"],
        //   },
        //   Ray: {
        //     // Rewrites the default setting fields
        //     settings: { ray: true },
        //     icon: Icons["ray.png"],
        //   },
        // },
      };
    },
    // Called after overlay mounted
    init() {
      // First pin is settled at the mouse position
      this.pins.push(new Pin(this, "p1"));
      this.pins[0].on("settled", () => {
        saveUserItems("pins[0] settled");
      });

      // Call when current tool drawing is finished
      // (Optionally) reset the mode back to 'Cursor'
      this.set_state("finished");
      this.$emit("drawing-mode-off");
      saveUserItems("pins[0] settled");
    },
    draw(ctx) {
      if (!this.p1) return;

      ctx.lineWidth = this.line_width;
      ctx.strokeStyle = this.color;
      ctx.beginPath();

      new VLine(this, ctx).draw(this.p1);
      ctx.stroke();
      this.render_pins(ctx);
    },
    use_for() {
      return ["VLineTool"];
    },
    data_colors() {
      return [this.color];
    },
  },
  // Define internal setting & constants here
  computed: {
    sett() {
      return this.$props.settings;
    },
    p1() {
      return this.$props.settings.p1;
    },
    p2() {
      return this.$props.settings.p2;
    },
    line_width() {
      return this.sett.lineWidth || 1;
    },
    color() {
      return this.sett.color || "#645eff";
    },
  },
  data() {
    return {};
  },
};
</script>
