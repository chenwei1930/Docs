# 补丁

From e8727d2b667c9c6b1a66ad29db4cea025a044894 Mon Sep 17 00:00:00 2001
From: "wei.chen" <wei.chen@rock-chips.com>
Date: Tue, 25 Jun 2019 19:29:59 +0800
Subject: [PATCH 1/2] arm: dts: rockchip: rk3126-linux-dpf.dts: support dpf
 customer

support customer digital photo frame

Change-Id: I6fd52f40528ea753a9e2c26af02a37e89f94fd8f
Signed-off-by: wei.chen <wei.chen@rock-chips.com>
---
 arch/arm/boot/dts/Makefile             |   1 +
 arch/arm/boot/dts/rk3126-linux-dpf.dts | 452 +++++++++++++++++++++++++++++++++
 2 files changed, 453 insertions(+)
 create mode 100644 arch/arm/boot/dts/rk3126-linux-dpf.dts

diff --git a/arch/arm/boot/dts/Makefile b/arch/arm/boot/dts/Makefile
index 1685901..1b3c3c3 100644
--- a/arch/arm/boot/dts/Makefile
+++ b/arch/arm/boot/dts/Makefile
@@ -524,6 +524,7 @@ dtb-$(CONFIG_ARCH_ROCKCHIP) += \
 	rk3126-bnd-m88-emmc.dtb \
 	rk3126-evb.dtb \
 	rk3126-linux.dtb \

+	rk3126-linux-dpf.dtb \
	 	rk3128-fireprime.dtb \
	 	rk3128h-box.dtb \
	 	rk3128h-box-avb.dtb \
diff --git a/arch/arm/boot/dts/rk3126-linux-dpf.dts b/arch/arm/boot/dts/rk3126-linux-dpf.dts
new file mode 100644
index 0000000..eb6428d9
--- /dev/null
+++ b/arch/arm/boot/dts/rk3126-linux-dpf.dts
@@ -0,0 +1,452 @@
+// SPDX-License-Identifier: (GPL-2.0+ OR MIT)// SPDX-License-Identifier: (GPL-2.0 OR MIT)
/*

 * Copyright (c) 2019 Fuzhou Rockchip Electronics Co., Ltd
 */

/dts-v1/;
#include <dt-bindings/gpio/gpio.h>
#include <dt-bindings/input/input.h>
#include <dt-bindings/pinctrl/rockchip.h>
#include <dt-bindings/pwm/pwm.h>
#include <dt-bindings/sensor-dev.h>
#include "rk3126.dtsi"
#include "rk312x-android.dtsi"

/ {
	model = "Rockchip RK3126 Linux board";
	compatible = "rockchip,rk3126";

# 1 chosen

```
	chosen {
		bootargs = "root=PARTUUID=614e0000-0000-4b53-8000-1d28000054a9 rootwait";
	};
```
# 2 reserved-memory

```
	reserved-memory {
		#address-cells = <1>;
		#size-cells = <1>;
		ranges;

		cma_region: region@88000000 {
			compatible = "shared-dma-pool";
			reusable;
			reg = <0x0 0x0>;
		};

		ramoops_mem: ramoops@62e00000 {
			reg = <0x0 0x0>;
		};
	};
```
# 3 adc-keys-1 

```
	adc-keys-1 {
		compatible = "adc-keys";
		io-channels = <&saradc 2>;
		io-channel-names = "buttons";
		poll-interval = <100>;
		keyup-threshold-microvolt = <3300000>;

		esc-key {
			label = "esc key";
			linux,code = <KEY_ESC>;
			press-threshold-microvolt = <1050000>;
		};

		play-key {
			label = "play key";
			linux,code = <KEY_PLAYER>;
			press-threshold-microvolt = <300000>;
		};

		right-key {
			label = "right key";
			linux,code = <KEY_RIGHT>;
			press-threshold-microvolt = <0>;
		};
	};
```
# 4  adc-keys-2

```
	adc-keys-2 {
		compatible = "adc-keys";
		io-channels = <&saradc 0>;
		io-channel-names = "buttons";
		poll-interval = <100>;
		keyup-threshold-microvolt = <3300000>;

		left-key {
			label = "left key";
			linux,code = <KEY_LEFT>;
			press-threshold-microvolt = <300000>;
		};

		volume-up {
			label = "volume up";
			linux,code = <KEY_VOLUMEUP>;
			press-threshold-microvolt = <0>;
		};

		volume-down {
			label = "volume down";
			linux,code = <KEY_VOLUMEDOWN>;
			press-threshold-microvolt = <1050000>;
		};
	};
```
# 5 backlight: backlight

```
	backlight: backlight {
		compatible = "pwm-backlight";
		pwms = <&pwm0 0 25000 0>;
		brightness-levels = <0 1 2 3 4 5 6 7 8 9 10 11 12 13 14 15 16
			17 18 19 20 21 22 23 24 25 26 27 28 29 30 31 32 33 34
			35 36 37 38 39 40 41 42 43 44 45 46 47 48 49 50 51 52
			53 54 55 56 57 58 59 60 61 62 63 64 65 66 67 68 69 70
			71 72 73 74 75 76 77 78 79 80 81 82 83 84 85 86 87 88
			89 90 91 92 93 94 95 96 97 98 99 100 101 102 103 104
			105 106 107 108 109 110 111 112 113 114 115 116 117
			118 119 120 121 122 123 124 125 126 127 128 129 130
			131 132 133 134 135 136 137 138 139 140 141 142 143
			144 145 146 147 148 149 150 151 152 153 154 155 156
			157 158 159 160 161 162 163 164 165 166 167 168 169
			170 171 172 173 174 175 176 177 178 179 180 181 182
			183 184 185 186 187 188 189 190 191 192 193 194 195
			196 197 198 199 200 201 202 203 204 205 206 207 208
			209 210 211 212 213 214 215 216 217 218 219 220 221
			222 223 224 225 226 227 228 229 230 231 232 233 234
			235 236 237 238 239 240 241 242 243 244 245 246 247
			248 249 250 251 252 253 254 255>;
		default-brightness-level = <128>;
	};
```
# 6 panel

```
	panel {
		compatible = "simple-panel";
		backlight = <&backlight>;
		enable-gpios = <&gpio1 RK_PB0 GPIO_ACTIVE_LOW>;
		enable-delay-ms = <120>;
		disable-delay-ms = <111>;
		unprepare-delay-ms = <100>;
		prepare-delay-ms = <100>;
		bus-format = <MEDIA_BUS_FMT_RGB666_1X18>;
		rockchip,data-width = <18>;
		rockchip,output = "rgb";
		width-mm = <154>;
		height-mm = <85>;
		status = "okay";

		display-timings {
			native-mode = <&timing0>;
			timing0: timing0 {
				clock-frequency = <45000000>;
				hactive = <1024>;
				vactive = <600>;
				hback-porch = <10>;
				hfront-porch = <210>;
				vback-porch = <10>;
				vfront-porch = <22>;
				hsync-len = <30>;
				vsync-len = <13>;
				hsync-active = <0>;
				vsync-active = <0>;
				de-active = <0>;
				pixelclk-active = <0>;
			};
		};

		port {
			panel_in_rgb: endpoint {
				remote-endpoint = <&rgb_out_panel>;
			};
		};
	};
```
# 7 pcfg_pull_up: pcfg-pull-up

```
	pcfg_pull_up: pcfg-pull-up {
		bias-pull-up;
	};
```
# 8 vdd_arm: vdd-arm

```
	vdd_arm: vdd-arm {
		compatible = "regulator-fixed";
		regulator-name= "vdd_arm";
		regulator-min-microvolt = <910000>;
		regulator-max-microvolt = <1390000>;
		regulator-always-on;
		regulator-boot-on;
		vin-supply = <&vdd_log>;
	};
```
# 9  vdd_log: vdd-log 

```
	vdd_log: vdd-log {
		compatible = "regulator-fixed";
		regulator-name= "vdd_log";
		vdd_log_gpio = <&gpio3 RK_PC1 GPIO_ACTIVE_HIGH>;
		pinctrl-names = "default";
		pinctrl-0 = <&vdd_log_pin>;
		regulator-min-microvolt = <1200000>;
		regulator-max-microvolt = <1200000>;
		regulator-always-on;
		regulator-boot-on;
		vin-supply = <&vcc_sys>;
	};
```
# 10 vdd_11: vdd-11 

```
	vdd_11: vdd-11 {
		compatible = "regulator-fixed";
		regulator-name = "vdd_11";
		regulator-min-microvolt = <1100000>;
		regulator-max-microvolt = <1100000>;
		regulator-always-on;
		regulator-boot-on;
		vin-supply = <&vcc_io>;
	};
```
# 11 vcc_io: vcc-io

```
	vcc_io: vcc-io {
		compatible = "regulator-fixed";
		regulator-name = "vcc_io";
		vdd_log_gpio = <&gpio0 RK_PA2 GPIO_ACTIVE_HIGH>;
		regulator-min-microvolt = <3300000>;
		regulator-max-microvolt = <3300000>;
		regulator-always-on;
		regulator-boot-on;
		vin-supply = <&vdd_log>;
	};
```
# 12 vcc_sys: vcc-sys

```
	vcc_sys: vcc-sys {
		compatible = "regulator-fixed";
		regulator-name = "vcc_sys";
		regulator-min-microvolt = <5000000>;
		regulator-max-microvolt = <5000000>;
		regulator-always-on;
		regulator-boot-on;
	};
```
# 13 vcc_host: vcc-host 

```
	vcc_host: vcc-host {
		compatible = "regulator-fixed";
		regulator-name = "vcc_host";
		vdd_log_gpio = <&gpio2 RK_PB2 GPIO_ACTIVE_HIGH>;
		regulator-min-microvolt = <5000000>;
		regulator-max-microvolt = <5000000>;
		vin-supply = <&vcc_sys>;
	};
```
# 14 wireless-bluetooth

```
	wireless-bluetooth {
		compatible = "bluetooth-platdata";
		uart_rts_gpios = <&gpio1 11 GPIO_ACTIVE_LOW>; /* GPIO1_B3 */
		pinctrl-names = "default","rts_gpio";
		BT,reset_gpio = <&gpio0 1 GPIO_ACTIVE_HIGH>; /* GPIO0_A1 */
		status = "disabled";
	};
```
# 15 wireless-wlan

```
	wireless-wlan {
		compatible = "wlan-platdata";
		rockchip,grf = <&grf>;
		wifi_chip_type = "rk912";
		clocks = <&hym8563>;
		clock-names = "clock_32k";
		WIFI,poweren_gpio = <&gpio3 11 GPIO_ACTIVE_HIGH>;/* GPIO3_B3 */
		WIFI,host_wake_irq = <&gpio2 1 GPIO_ACTIVE_HIGH>;/* GPIO2_A1 */
		status = "okay";
	};
};
```
# 16 sdio

```
&sdio {
	bus-width = <4>;
	max-frequency = <20000000>;
	cap-sd-highspeed;
	supports-sdio;
	ignore-pm-notify;
	keep-power-in-suspend;
	supports-rk912;
	status = "okay";
};
```
# 17 codec

```
&codec {
	#sound-dai-cells = <0>;
	spk-ctl-gpios = <&gpio2 RK_PB1 GPIO_ACTIVE_HIGH>;
	spk-mute-delay = <200>;
	hp-mute-delay = <100>;
	is_rk3128 = <0>;
	spk_volume = <25>;
	hp_volume = <25>;
	capture_volume = <26>;
	gpio_debug = <1>;
	codec_hp_det = <0>;
	status = "okay";
};
```
# 18 cpu0

```
&cpu0 {
	cpu-supply = <&vdd_arm>;
};
```
# 19 display_subsystem

```
&display_subsystem {
	status = "okay";
};
```
# 20 emmc

```
&emmc {
	bus-width = <8>;
	cap-mmc-highspeed;
	supports-emmc;
	disable-wp;
	non-removable;
	num-slots = <1>;
	status = "okay";
};
```
# 21 gpu

```
&gpu {
	status = "okay";
	mali-supply = <&vdd_log>;
};
```
# 22 hevc

```
&hevc {
	status = "okay";
};
```
# 23 hevc_mmu

```
&hevc_mmu {
	status = "okay";
};
```
# 24 i2c0

```
&i2c0 {
	status = "okay";
	hym8563: hym8563@51 {
			compatible = "haoyu,hym8563";
			reg = <0x51>;
			#clock-cells = <0>;
			clock-frequency = <32768>;
			clock-output-names = "xin32k";
	};
};
```
# 25 i2c2

```
&i2c2 {
	status = "okay";
	ts@40 {
		compatible = "gslX680-d708";
		reg = <0x40>;
		touch-gpio = <&gpio1 RK_PB1 IRQ_TYPE_EDGE_RISING>;
		screen_max_x = <800>;
		screen_max_y = <480>;
		revert_x = <1>;
		status = "okay";
	};
};
```
# 26 i2s_2ch

```
&i2s_2ch {
	#sound-dai-cells = <0>;
	status = "okay";
};
```
# 27 iep

```
&iep {
	status = "okay";
};
```
# 28 iep_mmu

```
&iep_mmu {
	status = "okay";
};
```
# 29 nandc

```
&nandc {
	status = "okay";
};
```
# 30 pinctrl

```
&pinctrl {
	vdd_log {
		vdd_log_pin: vdd_log_pin {
			rockchip,pins = <RK_GPIO3 RK_PC1 RK_FUNC_GPIO &pcfg_pull_up>;
		};
	};

	lcdc {
		lcdc_rgb_pins: lcdc-rgb-pins {
			rockchip,pins =
				<RK_GPIO2 RK_PB0 RK_FUNC_1 &pcfg_pull_none>, /* DCLK */
				<RK_GPIO2 RK_PB3 RK_FUNC_1 &pcfg_pull_none>, /* DEN */
				<RK_GPIO2 RK_PB4 RK_FUNC_1 &pcfg_pull_none>, /* DATA10 */
				<RK_GPIO2 RK_PB5 RK_FUNC_1 &pcfg_pull_none>, /* DATA11 */
				<RK_GPIO2 RK_PB6 RK_FUNC_1 &pcfg_pull_none>, /* DATA12 */
				<RK_GPIO2 RK_PB7 RK_FUNC_1 &pcfg_pull_none>, /* DATA13 */
				<RK_GPIO2 RK_PC0 RK_FUNC_1 &pcfg_pull_none>, /* DATA14 */
				<RK_GPIO2 RK_PC1 RK_FUNC_1 &pcfg_pull_none>, /* DATA15 */
				<RK_GPIO2 RK_PC2 RK_FUNC_1 &pcfg_pull_none>, /* DATA16 */
				<RK_GPIO2 RK_PC3 RK_FUNC_1 &pcfg_pull_none>; /* DATA17 */
		};
	};
};
```
# 31 pwm0

```
&pwm0 {
	status = "okay";
};
```
# 32 rga

```
&rga {
	status = "okay";
};
```
# 33 rgb

```
&rgb {
	status = "okay";
	pinctrl-names = "default";
	pinctrl-0 = <&lcdc_rgb_pins>;

	ports {
		port@1 {
			reg = <1>;
			rgb_out_panel: endpoint {
				remote-endpoint = <&panel_in_rgb>;
			};
		};
	};
};
```
# 34 route_rgb

```
&route_rgb {
	status = "okay";
};
```
# 35 saradc

```
&saradc {
	status = "okay";
	vref-supply = <&vcc_io>;
};
```
# 36 sdmmc

```
&sdmmc {
	cap-mmc-highspeed;
	supports-sd;
	card-detect-delay = <800>;
	ignore-pm-notify;
	keep-power-in-suspend;
	cd-gpios = <&gpio2 RK_PA6 GPIO_ACTIVE_LOW>; /* CD GPIO */
	status = "disabled";
};
```
# 37 tsadc

```
&tsadc {
	status = "okay";
};
```
# 38 u2phy

```
&u2phy {
	status = "okay";

	u2phy_otg: otg-port {
		status = "okay";
	};

	u2phy_host: host-port {
		vbus-supply = <&vcc_host>;
		status = "okay";
	};
};
```
# 39 usb_host_ehci

```
&usb_host_ehci {
	status = "okay";
};
```
# 40 usb_host_ohci

```
&usb_host_ohci {
	status = "okay";
};
```
# 41 usb_otg

```
&usb_otg {
	status = "okay";
};
```
# 42 vop

```
&vop {
	status = "okay";
};
```
# 43 vop_mmu

```
&vop_mmu {
	status = "okay";
};
```
# 44 vpu

```
&vpu {
	status = "okay";
};
```
# 45 vpu_combo

```
&vpu_combo {
	status = "okay";
};
```
# 46 vpu_mmu

```
&vpu_mmu {
	status = "okay";
};
```