/*
 * WiFiAnalyzer
 * Copyright (C) 2015 - 2026 VREM Software Development <VREMSoftwareDevelopment@gmail.com>
 *
 * This program is free software: you can redistribute it and/or modify
 * it under the terms of the GNU General Public License as published by
 * the Free Software Foundation, either version 3 of the License, or
 * (at your option) any later version.
 *
 * This program is distributed in the hope that it will be useful,
 * but WITHOUT ANY WARRANTY; without even the implied warranty of
 * MERCHANTABILITY or FITNESS FOR A PARTICULAR PURPOSE.  See the
 * GNU General Public License for more details.
 *
 * You should have received a copy of the GNU General Public License
 * along with this program.  If not, see <http://www.gnu.org/licenses/>
 */
package com.vrem.wifianalyzer.settings

import android.content.Context
import android.util.TypedValue
import androidx.appcompat.view.ContextThemeWrapper
import androidx.core.graphics.ColorUtils
import com.vrem.wifianalyzer.R
import org.assertj.core.api.Assertions.assertThat
import org.junit.Test
import org.junit.runner.RunWith
import org.robolectric.RobolectricTestRunner
import org.robolectric.RuntimeEnvironment
import org.robolectric.annotation.Config

@RunWith(RobolectricTestRunner::class)
class CharcoalThemeTest {
    @Test
    @Config(sdk = [29, 34], qualifiers = "notnight")
    fun charcoalWithSystemLightMode() {
        verifyCharcoal()
    }

    @Test
    @Config(sdk = [29, 34], qualifiers = "night")
    fun charcoalWithSystemDarkMode() {
        verifyCharcoal()
    }

    private fun verifyCharcoal() {
        val context = ContextThemeWrapper(RuntimeEnvironment.getApplication(), R.style.ThemeCharcoalNoActionBar)
        assertThat(color(context, android.R.attr.colorBackground)).isEqualTo(context.getColor(R.color.charcoal_background))
        assertThat(color(context, R.attr.colorButtonNormal)).isEqualTo(context.getColor(R.color.charcoal_interactive))
        assertThat(color(context, android.R.attr.textColorPrimary)).isEqualTo(context.getColor(R.color.charcoal_text))
        assertThat(context.getColor(R.color.charcoal_background)).isEqualTo(0xFF212121.toInt())
        listOf(R.color.charcoal_background, R.color.charcoal_surface, R.color.charcoal_interactive).forEach { surface ->
            assertThat(ColorUtils.calculateContrast(context.getColor(R.color.charcoal_text), context.getColor(surface)))
                .isGreaterThanOrEqualTo(4.5)
            assertThat(ColorUtils.calculateContrast(context.getColor(R.color.charcoal_secondary), context.getColor(surface)))
                .isGreaterThanOrEqualTo(4.5)
        }
        val disabled = context.getColorStateList(R.color.charcoal_text_states)
            .getColorForState(intArrayOf(-android.R.attr.state_enabled), 0)
        assertThat(disabled).isEqualTo(context.getColor(R.color.charcoal_disabled))
    }

    private fun color(context: Context, attribute: Int): Int {
        val value = TypedValue()
        assertThat(context.theme.resolveAttribute(attribute, value, true)).isTrue()
        return if (value.resourceId != 0) {
            context.getColorStateList(value.resourceId).defaultColor
        } else {
            value.data
        }
    }
}
