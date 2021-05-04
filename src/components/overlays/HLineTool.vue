<script>
// Line drawing tool
// TODO: make an angle-snap when "Shift" is pressed

import Overlay from "../../mixins/overlay.js";
import Tool from "../../mixins/tool.js";
import Icons from "../../stuff/icons.json";
import Pin from "../primitives/pin.js";
import Seg from "../primitives/seg.js";
import Line from "../primitives/line.js";
import HLine from "../primitives/hline.js";
import Ray from "../primitives/ray.js";

export default {
  name: "HLineTool",
  mixins: [Overlay, Tool],
  methods: {
    meta_info() {
      return { author: "C451", version: "1.1.0" };
    },
    tool() {
      return {
        // Descriptor for the tool
        //group: "HLines",
        icon: Icons["hline.png"],
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
      // Call when current tool drawing is finished
      // (Optionally) reset the mode back to 'Cursor'
      this.set_state("finished");
      this.$emit("drawing-mode-off");
    },
    draw(ctx) {
      if (!this.p1) return;

      ctx.lineWidth = this.line_width;
      ctx.strokeStyle = this.color;
      ctx.beginPath();

      new HLine(this, ctx).draw(this.p1);

      ctx.stroke();

      let x = this.layout.t2screen(this.p1.t);
      let y = this.layout.$2screen(this.p1.$);
      ctx.fillStyle = this.color;
      ctx.font = "16px Arial";

      // Helper labels
      ctx.fillText("hline", x + 10, y + 20);

      this.render_pins(ctx);
    },
    use_for() {
      return ["HLineTool"];
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
      return this.sett.lineWidth || 2;
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
