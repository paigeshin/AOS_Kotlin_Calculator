# AOS_Kotlin_Calculator

# Use views without `findViewById`

```xml
plugins {
    id 'com.android.application'
    id 'kotlin-android'
    id 'kotlin-android-extensions' <!-- add this line to gradle script -->
}
```

# Apply multiple gravity value

```xml
<TextView
        android:id="@+id/tvInput"
        android:layout_width="match_parent"
        android:layout_height="250dp"
        android:textSize="48sp"
        android:background="#efefef"
        android:maxLength="12"
        android:padding="10dp"
        android:text="0"
        android:gravity="right|bottom|center_vertical"
        />
```

# Entire Code

```kotlin
package com.example.calculator

import androidx.appcompat.app.AppCompatActivity
import android.os.Bundle
import android.view.View
import android.widget.Button
import android.widget.Toast
import kotlinx.android.synthetic.main.activity_main.*
import java.lang.ArithmeticException

class MainActivity : AppCompatActivity() {

    private var lastNumeric: Boolean = false
    private var lastDot: Boolean = false

    override fun onCreate(savedInstanceState: Bundle?) {
        super.onCreate(savedInstanceState)
        setContentView(R.layout.activity_main)

    }

    fun onDigit(view: View) {
        lastNumeric = true
        tvInput.append((view as Button).text)

    }

    fun onClear(view: View) {
        tvInput.text = ""
        lastNumeric = false
        lastDot = false
    }

    fun onDecimalPoint(view: View) {
        //마지막에 입력된 것이 숫자이고, decimal point가 아닌 경우에만 `.`을 추가할 수 있도록 허락한다.
        //딱 한 번만 입력 가능하다.
        if(lastNumeric && !lastDot) {
            tvInput.append(".")
            lastNumeric = false
            lastDot = true
        }
    }

    fun onOperator(view: View) {
        if(lastNumeric && !isOperatorAdded(tvInput.text.toString())) {
            tvInput.append((view as Button).text)
            lastNumeric = false
            lastDot = false
        }
    }

    fun onEqual(view: View) {
        if(lastNumeric) {
            var tvValue = tvInput.text.toString()
            var prefix = ""
            try {
                if(tvValue.startsWith("-")){
                    prefix = "-"
                    tvValue = tvValue.substring(1)
                }
                if(tvValue.contains("-")) {
                    val splitValue = tvValue.split("-")
                    var one = splitValue[0]
                    val two = splitValue[1]
                    if(!prefix.isEmpty()) {
                        one = prefix + one
                    }
                    val result = (one.toDouble() - two.toDouble()).toString()
                    tvInput.text = removeZeroAfterDot(result)
                } else  if (tvValue.contains("+")) {
                    val splitValue = tvValue.split("+")
                    val one = splitValue[0]
                    val two = splitValue[1]
                    val result = (one.toDouble() + two.toDouble()).toString()
                    tvInput.text = removeZeroAfterDot(result)
                } else if (tvValue.contains("*")){
                    val splitValue = tvValue.split("*")
                    val one = splitValue[0]
                    val two = splitValue[1]
                    val result = (one.toDouble() * two.toDouble()).toString()
                    tvInput.text = removeZeroAfterDot(result)
                } else if (tvValue.contains("/")) {
                    val splitValue = tvValue.split("/")
                    val one = splitValue[0]
                    val two = splitValue[1]
                    val result = (one.toDouble() / two.toDouble()).toString()
                    tvInput.text = removeZeroAfterDot(result)
                }
            } catch (e: ArithmeticException) {
                e.printStackTrace()
            }
        }
    }

    private fun removeZeroAfterDot(result: String) : String {
        var value = result
        if(result.contains(".0"))
            value = result.substring(0, result.length - 2)
        return value
    }

    private fun isOperatorAdded(value: String) : Boolean {
        return if (value.startsWith("-"))  {
            false
        }  else {
            value.contains("/") || value.contains("*") || value.contains("+") || value.contains("-")
        }
    }

}
```