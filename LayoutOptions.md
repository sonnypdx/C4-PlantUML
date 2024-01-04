# Layout Options

C4-PlantUML comes with some layout options.

- [📄 C4-PlantUML](README.md#c4-plantuml)
- [📄 Layout Options](#layout-options)
  - [Layout Guidance and Practices](#layout-guidance-and-practices)
    - [Overall Guidance](#overall-guidance)
    - [Layout Practices](#layout-practices)
  - [LAYOUT_TOP_DOWN() or LAYOUT_LEFT_RIGHT() or LAYOUT_LANDSCAPE()](#layout_top_down-or-layout_left_right-or-layout_landscape)
  - [LAYOUT_WITH_LEGEND() or SHOW_LEGEND(?hideStereotype, ?details)](#layout_with_legend-or-show_legendhidestereotype-details)
  - [SHOW_FLOATING_LEGEND(?alias, ?hideStereotype, ?details) and LEGEND()](#show_floating_legendalias-hidestereotype-details-and-legend)
  - [LAYOUT_AS_SKETCH() and SET_SKETCH_STYLE(?bgColor, ?fontColor, ?warningColor, ?fontName, ?footerWarning, ?footerText)](#layout_as_sketch-and-set_sketch_stylebgcolor-fontcolor-warningcolor-fontname-footerwarning-footertext)
  - [HIDE_STEREOTYPE()](#hide_stereotype)
  - [HIDE_PERSON_SPRITE(), SHOW_PERSON_SPRITE(?sprite), SHOW_PERSON_PORTRAIT() and SHOW_PERSON_OUTLINE()](#hide_person_sprite-show_person_spritesprite-show_person_portrait-and-show_person_outline)
    - [Using HIDE_PERSON_SPRITE()](#using-hide_person_sprite)
    - [Using SHOW_PERSON_SPRITE()](#using-show_person_sprite)
    - [Using SHOW_PERSON_SPRITE(sprite)](#using-show_person_spritesprite)
    - [Using SHOW_PERSON_PORTRAIT()](#using-show_person_portrait)
    - [Using SHOW_PERSON_OUTLINE()](#using-show_person_outline)
  - [(C4 styled) Sequence diagram specific layout options](#c4-styled-sequence-diagram-specific-layout-options)
    - [SHOW_ELEMENT_DESCRIPTIONS(?show)](#show_element_descriptionsshow)
    - [SHOW_FOOT_BOXES(?show)](#show_foot_boxesshow)
    - [SHOW_INDEX(?show)](#show_indexshow)
- [📄 Themes](Themes.md#themes)
- samples
  - [📄 C4 Model Diagrams](samples/C4CoreDiagrams.md#c4-model-diagrams)

## Layout Guidance and Practices

PlantUML uses [Graphviz](https://www.graphviz.org/) for its graph visualization. Thus the rendering itself is done automatically for you - that it one of the biggest advantages of using PlantUML.

...and also sometimes one of the biggest disadvantages, if the rendering is not what the user intended.

### Overall Guidance

1. Be minimal in the use of all directed relations - introduce the fewest possible directed `Rel_` and `Lay_` statements that achieve the desired layout. One way to do this is to immediately remove any of these you experiment with when they don't actually affect the layout at all. And of course you will remove the ones that affect it the layout in a negative way.
2. With dynamic rendering tools (e.g. VS Code plugin) do NOT trust the first rendering as it is shifty when adding code because you do not know exactly when it grabs the current unsaved code. Wait for a bit or close and reopen preview panel.

### Layout Practices

These are intended to correlate to the layout engine’s algorithm, but have (as of this writing) been determined by trial and error - not a code review.

Please read through all practices before starting.

1. Create all components, containers and boundaries first - in order top to bottom or left to right.
2. Use `Rel` (directionless) to create initial relationships.
3. If layout is not as desired, modify **some** Rel statements to contain direction `Rel_{direction}` to force shape layouts.
4. If the layout is not as desired, sparingly add `Lay_{direction}` to force any layouts that `Rel_{direction}` does not correct.
5. For both `Lay_{direction}` and `Rel_{direction}` statements used above:
   1. Exhaust attempts to get a working layout with `Rel_{direction}` before adding `Lay_{direction}`
   2. Try to introduce the fewest possible directed statements (of either type) that result in the desired layout.
   3. Immediately back out any directed statements that do not change the layout at all.
   4. Order inner objects first when it creates the desired result (enclosing objects tend to follow suit when child objects are ordered).
   5. When ordering multiple objects, only specify one relationship and, if possible in the same direction. For example if you want entity1 => entity2 => entity3, then `Rel_R(entity1,entity2)` and `Rel_R(entity2, entity3)` is the minimum possible statements and they all specify the same direction.
   6. Try NOT to apply directed statements to both inner elements and enclosing elements to force relationships that aren't working out.
   7. Make all orderings at the same nesting level whenever possible.
   8. Do NOT create duplicated, opposite direction statements in an attempt to force or ensure relationships as it does not affect the results. For instance if you have `Lay_R(entity1,entity2)` which is not working as desired and then also add the opposing one as `Lay_L(entity2,entity1)` - it does not help with forcing layouts to be as you want them. It might help to use `Lay_L` **instead of** `Lay_R`, but not both together.
6. Do not create an "All enclosing" boundary - the code for processing relationships seems to struggle with relationships inside this. Additionally, `SHOW_FLOATING_LEGEND()` will not display inside the All enclosing boundary.
7. Legend statements must come after at least one usage of each of the elements you want the legend to contain.

## LAYOUT_TOP_DOWN() or LAYOUT_LEFT_RIGHT() or LAYOUT_LANDSCAPE()

With the two macros `LAYOUT_TOP_DOWN()` and `LAYOUT_LEFT_RIGHT()` it is possible to easily change the flow visualization of the diagram. `LAYOUT_TOP_DOWN()` is the default.

```plantuml
@startuml LAYOUT_TOP_DOWN Sample
!include <C4/C4_Container>

/' Not needed because this is the default '/
LAYOUT_TOP_DOWN()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![LAYOUT_TOP_DOWN Sample](https://www.plantuml.com/plantuml/png/NP5FIyD04CNl-od6UaWAfQNIeqXZ53re6qngyHIssIbTs3zXTq8eudTtGhKLbuM1UM_U_9PTHfA1EclW9VjORwgwMXVrmtgxWbBQrg2uqgundKAupHUpV57dtf7K3iET4BC4Lfx08IfKiCD6TX613Ze2NpeWADpBpX0aC_4lFnqBKM28tgLIMUqcCCg6gYC5IJwCng8yHa9Rt_lEAHcEQJEVG7B2IiRmAO3F6IVjSLVBjkMSBUuWQrkZ6qdQkn6tyki9P6KnNJrMF18GRgPpU7tF1oqpnlSH63qmj8V6srQonJAuvbrGzPe80v2sQ7XNPBQl7xgKJYBd_DXO56zeVbzrmTf4dkJwN5L5oQx1T5OlCKz8ZD7ZKFR1sxyZItIA_-eR "LAYOUT_TOP_DOWN Sample")

`LAYOUT_LEFT_RIGHT()` rotates the flow visualization to *from Left to Right* and directed relations like `Rel_Left()`, `Rel_Right()`, `Rel_Up()` and `Rel_Down()` are rotated too.

```plantuml
@startuml LAYOUT_LEFT_RIGHT Sample
!include <C4/C4_Container>

LAYOUT_LEFT_RIGHT()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![LAYOUT_LEFT_RIGHT Sample](https://www.plantuml.com/plantuml/png/PL5RIyCm57xFhpZMXxKm9fCzYgoMkGbJnvefFfMiFK8WbvASKeRutprbSnFComVvhY7pI2fGPmsiyu_NdQpMYqTPRP-MAmcbigr1SQLTRRe6uQwOtHIpgl2Eb7OOxeNuPqep8JOOedUfQgnsOqZo0NMaeCY791Fb8HBQwi5thb7XaDRJCOoENQCClWJmENUaFUuhrRQSyuvxoDlMw5gHzYxXg-9w37cvcRmi95i2mkra2izlnS3bnlW-GXTv3P27sjjMiSHsXZHtWUmr4GOWRT5mL-HjtwTrAHr9pZd9c1HRDB-lkipQHNOohgJSbAmQH6Vs4hD4uXaz3l0PlFrhcQDh-0j-0000 "LAYOUT_LEFT_RIGHT Sample")

`LAYOUT_LANDSCAPE()` rotates the default flow visualization to *from Left to Right* like `LAYOUT_LEFT_RIGHT()` additional **directed relations** like Rel_Left(), Rel_Right(), Rel_Up() and Rel_Down() **are not rotated** anymore.

```plantuml
@startuml LAYOUT_LANDSCAPE Sample
!include <C4/C4_Container>

LAYOUT_LANDSCAPE()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")

System(S,"S")
System(SU,"S Up")
System(SD,"S Down")
System(SL,"S Left")
System(SR,"S Right")

Rel_Up(S, SU, "Up")
Rel_Down(S, SD, "Down")
Rel_Left(S, SL, "Left")
Rel_Right(S, SR, "Right")

SHOW_LEGEND()

@enduml
```

![LAYOUT_LANDSCAPE Sample](https://www.plantuml.com/plantuml/png/NP9VRu8m5CNV-HHtx45CY8kBZyiY0wCFJ0c5cJsHgdLhKjgcb12pxBllLb79-dBJtxdttCEVUMEfiMqj88q-DsLHfT4w8N6KBO3GMWlcFN2v5-s1mKiyUufdLQoafLmoy-fv_sU2iUTbp3HA1lHGSnc27xdA6skeLSOVU-JKM5PNRwgL1sfEmNuQmkYoQZI67m_mt5O47TjLL6lqsR8TH5eBlgUMA-aZYXz3Y4WsMIyA734CdYTJUF-8dHO9eRe6sWRJW5MmLxMcs5AtmdBS1KN7hMK6BA-Pm5qDPlljqmNs8g9FtuQYbpDnVQfxhBB1IQoheiW8ThccctgtMJABCJhcoj6eUZXoNKf2dznUK41Al4Af1oHn956T7B3KiPGTxO3bZkNywzlsiQjIepkW9GRMbvILCphJ16dlwhWpE_CKUU_i-DdmBEGetEp9QhEjqiLoiKxSrvypUS2_wGy0 "LAYOUT_LANDSCAPE Sample")

## LAYOUT_WITH_LEGEND() or SHOW_LEGEND(?hideStereotype, ?details)

Colors can help to add additional information or simply to make the diagram more aesthetically pleasing.
It can also help to save some space.

All of that is the reason, C4-PlantUML uses colors and prefer also to enable a layout without `<<stereotypes>>` and with a legend.
This can be enabled with `LAYOUT_WITH_LEGEND()`.

```plantuml
@startuml LAYOUT_WITH_LEGEND Sample
!include <C4/C4_Container>

LAYOUT_WITH_LEGEND()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![LAYOUT_WITH_LEGEND Sample](https://www.plantuml.com/plantuml/png/PL5RIyCm57xFhpZMXxKm9fCzYgpMiGbp5jivV2fPUuH0BYKvfGpnltlARe9v-I3VDMGUI7hgZ8Pr-lcsBQlTIxcgrelbOlCCXJIjHd6ZRAsx1k4XczrbiofpbgIow1-5-E-A4o5ozC7PM3P6sJ54wO0ga9VaV9I8uX08JVNaEjj8VuZhwHX6nx9H0jy2-5nAuXxtbMnRpjdX7jAsrQgMf9oD-2gx7KDQv9FDecIBHxYVJE7r8nkuL6lN1-W2hm5oK3lJIfQOJfFYBYXxHOGUI1dKt1LusyzfNKn7adDECYR5E-hpgwwpje6TZAkop0jM3Q8BUurP8l6C7WVuyixyjSpHDlm9lm00 "LAYOUT_WITH_LEGEND Sample")

Instead of a static legend (activated with `LAYOUT_WITH_LEGEND()`) a calculated legend can be activated with `SHOW_LEGEND(?hideStereotype, ?details)`.

The calculated legend has following differences:

- only relevant elements are listed
- custom tags/styles are supported
- stereotypes can remain visible (with `SHOW_LEGEND(false)`)
- details can be displayed in different sizes via the `$details` argument
  - `$details = Small()` .. default; details are displayed with a smaller size compared to the legend labels
  - `$details = Normal()` .. details and labels are displayed with same size
  - `$details = None()` .. only the labels are displayed
  - if `$legendText` contains `\n` then the text before is the label and the text behind the details
- **`SHOW_LEGEND()` has to be last call in the diagram**

```plantuml
@startuml SHOW_LEGEND Sample
!include <C4/C4_Container>

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")

SHOW_LEGEND()
@enduml
```

![SHOW_LEGEND Sample](https://www.plantuml.com/plantuml/png/JL1DIyD04BtlhnZZeGc4IgL7aSOOseFMO59x3DjaX8Nz2BiJGX7_knDQs-xbmRwFUJEhGDBJO3HKcuzzyrQiY-qhLDBq6iMTigqUEeIdVFcGBvlSMPBAedyMeaGVd8rbPvHD8SecL86y9EUZH5J7G6YQ5pVOJlfZt2vIc9rYPmdy2E1tYOj7F3IoxpbdZmV8-bwhLf9oDkAl_3w5h2hdsw9cYqTud2_W_IkVk4nhDmOO0hS1Sj0wqqkMc46JubbGZue8FP0og7bMu6w_vtOndKZEESkO59-e_xUwrje5TZ9kwhgiM3M9BkmrPet4DKQSuDixSsiHDmUE4x52s_7P_m00 "SHOW_LEGEND Sample")

Legend labels and details can be defined via `\n` in `$legendTest` arguments too.

```plantuml
@startuml SHOW_LEGEND Sample, $legendText defines legend details
' convert it with additional command line argument -DRELATIVE_INCLUDE="./.." to use locally
!if %variable_exists("RELATIVE_INCLUDE")
  !include %get_variable_value("RELATIVE_INCLUDE")/C4_Container.puml
!else
  !include <C4/C4_Container>
!endif
' $legendText with \n defines the label and details of the legend entry ("backend container" is label, "eight sided shape" is details) 
AddElementTag("backendContainer", $fontColor=$ELEMENT_FONT_COLOR, $bgColor="#335DA5", $shape=EightSidedShape(), $legendText="backend container\neight sided shape")
' $legendText without \n defines only a label 
AddRelTag("async", $textColor=$ARROW_FONT_COLOR, $lineColor=$ARROW_COLOR, $lineStyle=DashedLine(), $legendText="async call")
' if no $legendText defined, $tag is automatically the label and all additional displayed properties are the details
AddRelTag("sync/async", $textColor=$ARROW_FONT_COLOR, $lineColor=$ARROW_COLOR, $lineStyle=DottedLine())

System_Boundary(c1, "Internet Banking") {
    Container(mobile_app, "Mobile App", "C#, Xamarin", "Provides a limited subset of the Internet banking functionality to customers via their mobile device")
    Container(backend_api, "API Application", "Java, Docker Container", "Provides Internet banking functionality via API", $tags="backendContainer")
}
System_Ext(banking_system, "Mainframe Banking System", "Stores all of the core banking information about customers, accounts, transactions, etc.")

Rel(mobile_app, backend_api, "Uses", "async, JSON/HTTPS", $tags="async")
Rel_Neighbor(backend_api, banking_system, "Uses", "sync/async, XML/HTTPS", $tags="sync/async")

SHOW_LEGEND()
@enduml
```

![SHOW_LEGEND Sample, $legendText defines legend details](https://www.plantuml.com/plantuml/png/hLLDZzf84BtxLzGuYHPBXD4goIr48U1i9c9WXCbk3f6iibr0A-rkgxlDZhNQ_vvgcmypZBINlI2gkwhwlLUluADrQ5nTIaY-BFzAv_4VyM862PQLf26ybBGbLQpfqK516w78GdV4eKCXRV0Rv5hjoJWG3luMRWTO5C89hL3oLLaY9qkk13JRkYJbuFLi5Sydwxi_u_HkCPr_cyNZS7Gx6eNWDDIMGEeSfMo26x61Ltiq0ZD9AJqAw-mWV5eSHW70ZL2vh0k2LrjowQbcZxAcvqfkfs_JgLRCGP4PLIn0S4FIKh_L--dRYxGFdA8AiM7EVM5QqZ_KII2tOmQOaGGywmHwqrrqwh4Cfe51c67-qyVvyOqGXEsgXn2IsEuSM55G0NQ75RMtXuuH19EYY2LvJTUuFJKxuGrvW1iEfbfgCtuPp-FxUB5EFo_vOxgSBrTydssxs_35cpVlPfDtlgPzQXpxnnF_TkBZGNHXX_4rz1_g6c_qZ5QwTdsvj98Du44nJsb5igM3jb6vn-Euyi1XibgnIoyOU6zTtFOl4jT86i_GxgYOStp5edq4lDrQh6mufP-nVU5XuDRBZxNJ9JhHMlJ9iFceR_z2s4fYmsfKHbUy8uBveg6swhX0FSOUo-t_I5ixTwGT1K7IM4Tb-adNga3J3FBVsM9tof5HvE0Jgfz2RSC8_k4T03YvQ53gJF0gOLLn_dqRmAIg668uVJ64xrZojYaVFXYzv-5RFqvH2kTjK6UMkn_SVtejwrw3JQtoJYpX6h_-UMrPNZ8MzW9zZJ3G0M31zYAdTjlxy0u-P7o28KmUxZmuoUFnVJsghxZ78SmqfndeBySPxt_Wyb2uSTYPm8wlDoqA_Ztg6p-wmQ5DQjiZBnkdRGoMTDGPkcoF8d7QUCtOEmUPSZuuGU52RSgM3M3cb-UaqH0mptcSZhyvWyfY2veZSlc8GGNih8lvNOhrpPBr25hB3U5hibpSVbcl7v8pqywDaM-KBln-P_g9v5TSZrtFRcQJtC-VTkwPtPlp_EypY8AFt9r_ady1 "SHOW_LEGEND Sample, $legendText defines legend details")

Legend details can be deactivated via `SHOW_LEGEND($details=None())`

```plantuml
@startuml SHOW_LEGEND Sample, hide details with $details=None()
' convert it with additional command line argument -DRELATIVE_INCLUDE="./.." to use locally
!if %variable_exists("RELATIVE_INCLUDE")
  !include %get_variable_value("RELATIVE_INCLUDE")/C4_Container.puml
!else
  !include <C4/C4_Container>
!endif
' $legendText with \n defines the label and details of the legend entry ("backend container" is label, "eight sided shape" is details) 
AddElementTag("backendContainer", $fontColor=$ELEMENT_FONT_COLOR, $bgColor="#335DA5", $shape=EightSidedShape(), $legendText="backend container\neight sided shape")
' $legendText without \n defines only a label 
AddRelTag("async", $textColor=$ARROW_FONT_COLOR, $lineColor=$ARROW_COLOR, $lineStyle=DashedLine(), $legendText="async call")
' if no $legendText defined, $tag is automatically the label and all additional displayed properties are the details
AddRelTag("sync/async", $textColor=$ARROW_FONT_COLOR, $lineColor=$ARROW_COLOR, $lineStyle=DottedLine())

System_Boundary(c1, "Internet Banking") {
    Container(mobile_app, "Mobile App", "C#, Xamarin", "Provides a limited subset of the Internet banking functionality to customers via their mobile device")
    Container(backend_api, "API Application", "Java, Docker Container", "Provides Internet banking functionality via API", $tags="backendContainer")
}
System_Ext(banking_system, "Mainframe Banking System", "Stores all of the core banking information about customers, accounts, transactions, etc.")

Rel(mobile_app, backend_api, "Uses", "async, JSON/HTTPS", $tags="async")
Rel_Neighbor(backend_api, banking_system, "Uses", "sync/async, XML/HTTPS", $tags="sync/async")

SHOW_LEGEND($details=None())
@enduml
```

![SHOW_LEGEND Sample, hide details with $details=None()](https://www.plantuml.com/plantuml/png/hLLDRzf04BtxLqpSLCKI9QgItaeL0cwRY424wSUXaZMs5rXblMljhccigl-zixOX1YBrqWloUkVZlJTlyBMng6sP2uY-pB_7q_1pE9j0X7aXM1ysF6EGCOjS6FZ5xGREszDmfYJh1TuRI9NSCcs1soO2iunRhYGAkifpb1a8BXcWNfSvanRUJXRXTBIy_HR6jxFnzEia7Fg3Yy701wkWD0o4Ib68oZlZAtYzHSqn4IncJznOq_EFa_t00pZZCXKbWNszPZRUvsnHbEobb8lnLJnMagX8fWS5yVVEc32iM-h3-EeWx2E5o8olYFEvO6jwNhAdblHFIJAjACg0tH03J9W0Htqddber5tKUa0owWfwVOFhepkckXm_SDDbzy1bVRomOca46PeC5gs_RYW5ueomB1NEQBd6zBxR7w_VXV4M7iH9A3y_3QNWVpfRnfpdzZEVJ-OBkatLpwx-wl7m_6RrtENMhOUYQHwvtvCwzeDzbFJo5_bEUuWrUq4gLjYkNag82R1Lpb1PCr7JGL39rU2nbjXn6YmMPz821yzR1RVSYifLWmmcQ3SkcN9wog9k0irkDbGmdrG7Y1cRcOE3QoO-bLJbQNblqQDZqgcl_Z9j2O4Lg55eLj2ES-A9cTLOxnYvZX-NYFz9Mrkve1vuNLSQoFBvHfSnGLxtq7LdiLbgc9RDmW_AHoxKVm6_Q0O2zYtgvIZYj4XO5nT_N1nWL1K7qnw_wy0DpsZRfZWzQRMdunesJvzmw6vI9eUgj-_VTagORh4gPDc9nMxdrJqj3yZ9jOClHvN0D3G0IRCjJLczx5rxhGyB72SBeuTQ14pGULzUXkiCjzc6YA4n3TpcUyVu3buD2XVt616Pukcc1zsUdR_XaUssPsDIld6mKjjAOivtEq4Gx595Lscb6tcbbIkd57WebAftNR00JjpnxZVg0QKhZjFHaDKg3DMWwCPiE29H7pZgOtw5ONmqp3a5jkJxSHVFPnPVbyY5wPjguCN25ufdRxqGTINx2TLVrsSraallfSUMEsPqvdpy-lQDFJE1TKpFwX_uB "SHOW_LEGEND Sample, hide details with $details=None()")

## SHOW_FLOATING_LEGEND(?alias, ?hideStereotype, ?details) and LEGEND()

`LAYOUT_WITH_LEGEND()` and SHOW_LEGEND(?hideStereotype)` adds the legend at the bottom right of the picture like below and additional whitespace is created.

```plantuml
@startuml Layout With Whitespace Sample
!include <C4/C4_Container>

Person(a, "Person A")
Container(b, "Container B", "techn")
System(c, "System C")
Container(d, "Container D", "techn")
Container_Ext(e, "Ext. Container E", "techn")

Rel_R(a, b, "calls")
Rel_D(b, c, "uses")
Rel_D(c, d, "uses")
Rel_R(d, e, "updates")

SHOW_LEGEND()
@enduml
```

![Layout With Whitespace Sample](https://www.plantuml.com/plantuml/png/LP11YuCm48Nl-HLJJn5Az-AnBDrLsHvAk-Z1esIJ0OKOnKoW_VUTh63hQMQ-UOzvpCcJcYaC5YxgCGQ2fgSEcgudz9FI2BKQ9ejYrpjjWq4uvjb7dhNvw4Zr3kTF8NvnzgEJQW_9qi9NaefL8VzuiKxmdV18g3l7elhX2GUf6Iqjv1khsLYBT-kAs_9E4dd3zG0lTVck5XNQjeePOngjhFLC8onYlXWWU7mn1cRBgXWcdWcJKVIFHNs-DUsb_2clXKp52PtXJpu1 "Layout With Whitespace Sample")

Therefore a floating legend can be added via SHOW_FLOATING_LEGEND(), positioned with Lay_Distance() and existing whitespace is reused like below.

- `SHOW_FLOATING_LEGEND(?alias, ?hideStereotype): shows the legend in the drawing area
- `LEGEND()`: is the default alias of the created floating legend and can be used in Lay_Distance() call

```plantuml
@startuml Compact Legend Layout Sample
!include <C4/C4_Container>

Person(a, "Person A")
Container(b, "Container B", "techn")
System(c, "System C")
Container(d, "Container D", "techn")
Container_Ext(e, "Ext. Container E", "techn")

Rel_R(a, b, "calls")
Rel_D(b, c, "uses")
Rel_D(c, d, "uses")
Rel_R(d, e, "updates")

SHOW_FLOATING_LEGEND()
Lay_Distance(LEGEND(), e, 1)
@enduml
```

![Compact Legend Layout Sample](https://www.plantuml.com/plantuml/png/LP31QiCm44Jl-egjJpAKXaAEeIILtRHWac87SXIgj3G6MJQM1CdVTvLG9pvfz-qC6cRjWnf3x2o8lXkK3b3XBpe3bRhqCK2Zki4YUsgTjj4WhCHo8PPIz2werk7unjWtZhvtN3r3TXjXa-LiKl0VEamRl6Uq1jGdHwBcuWDsN1EwZI1cLZEp5e_M2Slo73ZIXTuNkAlBHpMhqSewPKnfjBBM4qsmIFbIWEZnpWYOEQjJcFHD78mALyoQp_rHVbJxpU5hjvLLkIrt1SyPLIUBbefr6la_lPfVSxQcSgdkFm00 "Compact Legend Layout Sample")

## LAYOUT_AS_SKETCH() and SET_SKETCH_STYLE(?bgColor, ?fontColor, ?warningColor, ?fontName, ?footerWarning, ?footerText)

C4-PlantUML can be especially helpful during up-front design sessions.
One thing which is often ignored is the fact, that these software architecture sketches are just sketches.

Without any proof

- if they are technically possible
- if they can fulfill all requirements
- if they keep what they promise

More often these sketches are used by many people as facts and are manifested into their documentations.
With `LAYOUT_AS_SKETCH()` you can make a difference.

```plantuml
@startuml LAYOUT_AS_SKETCH Sample
!include <C4/C4_Container>

LAYOUT_AS_SKETCH()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![LAYOUT_AS_SKETCH Sample](https://www.plantuml.com/plantuml/png/NL5RIyCm57xFhpZMXxLG9fCzYgoMuS1RiPdYKydQ8mHoASafPOZ_tLCsDp4l7-IxXgmYgK23DV1OVBniPLFKJVsmbkK6QcLxW-92kzOC7S9DkRmgbqtf7IdjCDmAyT-JPa9K6A9tgUgiTZaanOGwKb3aGvA9UXy9RNFd1zUfi4_RHGwpGzKiWoy1V4uLwOYxHlKzvxpZ3eg-DxfLfBrB-AgypA6eg_dpMh8b85pF5_3qLavSOOmV8moHrm1vQBtj5KliO4Xp5yXH4s40qXODTqNUzdrSbzA1v9oZZ4dnYkRtLUTPsyXEnesKLSsgINHYpp7tI3nZn0a-WxT_BIjq7N_03m00 "LAYOUT_AS_SKETCH Sample")

Additional styles and the footer text can be changed with SET_SKETCH_STYLE():

- `SET_SKETCH_STYLE(?bgColor, ?fontColor, ?warningColor, ?fontName, ?footerWarning, ?footerText)`:
  Enables the modification of different sketch styles and footer.

The possible font name(s) depend on the output format (e.g. PNG uses fonts which are installed on the server and SVG fonts have to be installed on the client).
Additional is it possible to define comma separated fall back fonts (if the diagrams are exported as SVG. Atm
PNG does not support fallback fonts based on a PlantUML [bug](https://forum.plantuml.net/14842/specify-fall-back-fonts-is-not-working), but this could be fixed in one of the following versions)

```plantuml
@startuml LAYOUT_AS_SKETCH Sample
!include <C4/C4_Container>

SET_SKETCH_STYLE($bgColor="lightblue", $fontColor="darkblue", $warningColor="darkred", $footerWarning="Sketch", $footerText="Created for discussion")

' PNG with font jlm_cmmi10 (typically another font is used)
' SET_SKETCH_STYLE($fontName="jlm_cmmi10")

' SVG with fallback fonts MS Gothic,Comic Sans MS, Comic Sans, Chalkboard SE, Comic Neue, cursive, sans-serif (typically without "MS Gothic")
SET_SKETCH_STYLE($fontName="MS Gothic,Comic Sans MS,Comic Sans,Chalkboard SE,Comic Neue,cursive,sans-serif")

LAYOUT_AS_SKETCH()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

PNG with font `jlm_cmmi10`

![LAYOUT_AS_SKETCH with custom style png Sample](https://www.plantuml.com/plantuml/png/VLDTRzD047pthrXCfJYIAGJraQ0QAqebsX1nRgi-MMTxanov3-lkJ1eX_ZjxTH871FZbx9tPsTdRzRNptFfEIRZDdhxS5sN6IlPvLkGti1D-0tNdl57W_5uYj7eDZAjMOlHAw5fs3SA7_EfjVbNcHdikDDgFKSHcnK6ZPCNJxIovgDQvaSPEOodM6r_93kCKBbQKSuWtt6wFuHstMkZr6MAnwVd6ettiuMdCjkZhpG0K-EodSMwHUsnWPImqmf5_9uoEnr4qWkLYtdSLAiCtgSfQAJ5v1udVjwBcKkw1Q-CtQ7kASD0vRCP_QIdW2wvm6WywVHNsSAn2WXMljozQ3kuOp4bRr6bkbAZf9dK8fZ1yqlk6osrbk6s0pOxG0ZjCQHJMYU_queZvng4LgtFZeQJfFCIdEcI7tFp7-RySdHdwtS-PdQERmKpe_iybIYYsHEkCJdYZX4uXpi8fdBVS6qivREyyglAJwJJDUf_KanH6_PQDnl0Z0dfEsvNii2fvsvBE8rQGjQsazdsOC8NorobaR7cvc1MKOX7UNqxWxY4FM2QbsRsCqphm1cgZMauKrKalg1OKT86qHU25Gacr77dxUN2N-1uadGCjDFiLvR6hmTQzeqmwRufYoOWLI2Tqa9aZROJVOJXMrgZpb6lK3VsHlm00 "LAYOUT_AS_SKETCH with custom style png Sample")

SVG with fallback fonts MS Gothic,Comic Sans MS,Comic Sans,Chalkboard SE,Comic Neue,cursive,sans-serif

![LAYOUT_AS_SKETCH with custom style svg Sample](https://www.plantuml.com/plantuml/svg/VLF1RjD04BtxAmPJAOva2a4z4bHZHQb46oBijkh9MjkJPCckrzeTDqI8VsUsJk800b_MdddptfkTyRKZOQdJ2cwJfo_tUP5aHVPvbgSti9EqWQfpP3GusYi4zxo6JEXMOV1ADfNgQeGFwTNRzAf8JKD2DcW_1a4somySHPO_tSwYYtAT6cNiD5HolQ5ITHZ6SB7YcaEy5dPx3E-4RMIpFijOh7ky8RIFVNeQPbkaQZCaSlnEqp2rA0XhM1aBjNJitqdJXECW6C5oCU-xyihmJUcYqbfEta54-rPMGgazYCRG1cqFaGuwX_MOQ_zkoYCMGkCq79XwdUpXgCEKfQYsBsmExZAOCxkiujHeMV5TDZuOm_39xnkXjgKHjcRDOsg17SOy3ElaCxyuHhvnQENgtBgND1r1UD9XE__p_Iy_PtP-TtDcvkXbiE9x_tE98eujqJhJHABMieaXJFmf7Lb1ndg3UqUeYq-cQtZM-wYQn33gjsmqXXy1y7FQhcY7PI7QbdaUiOIaRHKtJtx274fVnv1aoyl5BESIY_3-SW9t3wdF9KgPtSiuhGCoK1dT2eReJf5aBSZv-dYBWAH6nLgElVqyk8keJpBF0UQR_OhgsDLWwzvn9PytURxC6EL1f-n0CqVU1zgXFrRMwFEIQsng_YD_0G00 "LAYOUT_AS_SKETCH with custom style svg Sample")

All available (PNG) fonts can be displayed with

```plantuml
@startuml
listfonts
@enduml
```

## HIDE_STEREOTYPE()

To enable a layout without `<<stereotypes>>` and legend.
This can be enabled with `HIDE_STEREOTYPE()`.

```plantuml
@startuml HIDE_STEREOTYPE Sample
!include <C4/C4_Container>

HIDE_STEREOTYPE()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![HIDE_STEREOTYPE Sample](https://www.plantuml.com/plantuml/png/NL1RIyCm57xFhpZMXxKm9fCzYgpMudnGoneLdqhM7Y6GIqbEAKFyxvwokObv-I3VDLb7Ke46Qs3pU5ysjIotvOlyg4gebUqDYWljMZDq23V5wgfODOLtfBJ3S2l4FqkQ2L5XYDwbghFQpI799zIHWY8VaapK-qXecpi_k4w5VTekvp0xDCqo-1B0vzIGZhXhLDzppZlk8Ezxerj5shk4huhBEUHrjNWk9Li2mlLY2KzlnSJbnlWnmX1v3P27rjjUiSGEXZHtWHmr4GOWRT5mL-HjtyTrAHr8pZdAc1HRDB-lEizwZUnat4XPrQoQH2Vs7FE0n3D6dE0pUFlNiaRNyVV_0000 "HIDE_STEREOTYPE Sample")

## HIDE_PERSON_SPRITE(), SHOW_PERSON_SPRITE(?sprite), SHOW_PERSON_PORTRAIT() and SHOW_PERSON_OUTLINE()

With the macros `HIDE_PERSON_SPRITE()`, `SHOW_PERSON_SPRITE()` and `SHOW_PERSON_PORTRAIT()` it is possible to change the person related default sprite or person layout itself. `SHOW_PERSON_SPRITE()` is the default.

- **HIDE_PERSON_SPRITE()**: deactivates the default sprite
- **SHOW_PERSON_SPRITE()**: activates the default sprite "person"
- **SHOW_PERSON_SPRITE($sprite)**: activates a specific sprite as default sprite
- **SHOW_PERSON_PORTRAIT()**: activates portrait instead of a rectangle
- **SHOW_PERSON_OUTLINE()**: activates person outline instead of a rectangle

"person" and "person2" are predefined sprites which can be used as default sprite too.

```plantuml
@startuml predefined sprites Sample
!include <C4/C4_Container>

Person(userA, "User A", "with predefined sprite person", "person")
Person(userB, "User B", "with predefined sprite person2", "person2")
@enduml
```

![predefined sprites Sample](https://www.plantuml.com/plantuml/png/SoWkIImgAStDKIWeIar9JSlCIqrHA2ueoYn9BLO8Jimjo4dbKipCIyufJKbLi74sqNSsYNVEpoj912eiikFY2aWjAixFqoWjJYror55G2WNI2evAG5PvPaa6ffaA1M3r80LGbYQo4Kum8vm86c64CCC8Q8X3QbuAq1S0 "predefined sprites Sample")

### Using HIDE_PERSON_SPRITE()

```plantuml
@startuml HIDE_PERSON_SPRITE Sample
!include <C4/C4_Container>

HIDE_PERSON_SPRITE()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![HIDE_PERSON_SPRITE Sample](https://www.plantuml.com/plantuml/png/PL7BIiD05DtFLrpZeWc4IgLBaSOOR1VMq4HTXcboXO5vX9aRGX7_tHjQsuApEJ3dESmgaFGq60thpNFHbCMkUjisLRdRr0LKqlGQnOsohHuwX8TyUPSlcznPaigYVnJYlojEX2ZH1sTZsHbbKuYo2LKWBydvA17L8H2QviaDjfF-4BUB56R7iba2tmBud4lY4VUDx7lE-SGzP7slLIj9EHlnLNwRGbQLysrHiyKZtCyNyFgHJrocjHi3387N03bedUabIyoWIN4NrACYGW-a36hk2hpjvxGkfYF9EISPasA7-kzLbrdlWPsCwxekArPDeZDxYNb1uXaZJl3bdRcshD1s_0c_ "HIDE_PERSON_SPRITE Sample")

### Using SHOW_PERSON_SPRITE()

```plantuml
@startuml SHOW_PERSON_SPRITE Sample
!include <C4/C4_Container>

/' Not needed because this is the default with sprite "person" '/
SHOW_PERSON_SPRITE()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![SHOW_PERSON_SPRITE Sample](https://www.plantuml.com/plantuml/png/PP7HQy8m4CRVyrVSkeTM4CNXunXsHUOUfiLsyx74ziH0ceJaIf6n_ttNwNIm43Z8zztTxoEBGD9JrseeLfjTbI-tnMPT5VdsjLn28LkdKTmfK-kkGNZCvjDiNcNMa5G6_PCGqnZMbi0WDjZ07clP1GGwgW1ywOZGu45scg1NT8JWl2A4oA4FraGGJyN_lSb8YFp7aCYcLMOCKJfK5SXBiZuQYU8K2DlgsNQcaVwKrBCnn6VSU0IV0lXSCPCUzvLqZkViS0-fSrhLaXG3y5DsFuQqo2VhPSaj7k5XCeEtZspGKgrj7u0ZUGvZeRQjasnfEP3YNL1oAa8Ff5hKl2imszU5Bg6po7CkDXR55lLlgXlMU-1Ehgkop0js3QQhUXlpWiGOFGxbu6txjsM1fk5l_0O0 "SHOW_PERSON_SPRITE Sample")

### Using SHOW_PERSON_SPRITE(sprite)

```plantuml
@startuml SHOW_PERSON_SPRITE(sprite) Sample
!include <C4/C4_Container>
!define osaPuml https://raw.githubusercontent.com/Crashedmind/PlantUML-opensecurityarchitecture2-icons/master
!include osaPuml/Common.puml
!include osaPuml/User/all.puml

SHOW_PERSON_SPRITE("osa_user_green_architect")

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![SHOW_PERSON_SPRITE(sprite) Sample](https://www.plantuml.com/plantuml/png/RL1DIyD04BtdLuprO0erGV4a8jPGLF0ZDD4UmpQPjWlxnUw4KCJ_xYojhQ1xcM7VctbltbqWuQdN2gh7jqLJpkRLssjJbVEdUfO6voNX62gXdSBaH9fMzHt2JN6L5rTDOGq9QT3V9YSThhW36qGPbss8NBZESo-6R2rfqo_xWBxb0JIKjLRdXHTXWvsMfijB9GozlpoVMuScODkpx5RuTiFgBVKUByybpuPSYq3eZqRsSdbXjROcSzp_1TzPEHTAxU3adpj7J6sYmMRj4Krpa1wDawH47wn9HRGwWT4qLXd82xAUyMhBbdHpRtlJ2RzDsui9dEq2Enl3PmBy3a6b0ouRuHplMU0IfiufsGgIrepugpYTmBGgizTPpICUuJAxW9UF8c9JfUmG89eCG1OuGYUOedj5ahMW7YHnDa1IesAjmDw-zkvIse6yPq-BXyrH_LnrjCLXXQZtMDTbnQn8Eg37DGz8R6F0M5RUwjyZTsWwJleR "SHOW_PERSON_SPRITE(sprite) Sample")

### Using SHOW_PERSON_PORTRAIT()

```plantuml
@startuml SHOW_PERSON_PORTRAIT() Sample
!include <C4/C4_Container>

SHOW_PERSON_PORTRAIT()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

' if a person is combined with a sprite then the rectangle layout is used again
Person(person, "Person with sprite", $sprite="person2")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![SHOW_PERSON_PORTRAIT() Sample](https://www.plantuml.com/plantuml/png/RL9BJuD04BxlhnZHf3HfQjfuL5CaZVLWImhQ8zd2jDrasIMxGqXZ_EyEqeS7EUp0plUORyBCatJKL1hInMgJ9_DrkbhcoMgThQEtB1n2AgjQexXHfj1DYV0OFzp73tbi3Kbbq3qByJzJY0ITjoQKPQNC28AegygJasHTC1JfqHDM-OjjJ2dTCImc8nZqTeCXV0dWvs8JjhZDPLsppWQt4DMrLeKaPKt0L_7j2A8q6I_d6LCSmdGyWVVFkEj5MjlMG-Dv6Y0BXQrgoP2gqQJO2x9M4Q43KXLgzl8ys_Tfkf3w9kkSODmK0r0xa53_vWFbEyqjCqjWp84xldQA4EY0fZl0OK7IxDbCowDjgEFmG2N8FQSxBwhNOwl-knVhfJZ8NV_s5FImQJV86lLvlTVzV7YEm7MHPKdAg0vqwLxpl2BnFbhioixPwYzbXgRaV-87 "SHOW_PERSON_PORTRAIT() Sample")

### Using SHOW_PERSON_OUTLINE()

> This call requires PlantUML version >= v1.2021.4!

```plantuml
@startuml SHOW_PERSON_OUTLINE() Sample
!include <C4/C4_Container>

SHOW_PERSON_OUTLINE()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample') {
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
}
System(twitter, "Twitter")

' if a person is combined with a sprite then the rectangle layout is used again
Person(person, "Person with sprite", $sprite="person2")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![SHOW_PERSON_OUTLINE() Sample](https://www.plantuml.com/plantuml/png/RL9BQy904BxdLqpJWX74elJO5jCWjT2gc5YFOKr6NTZiXjq9GKh_UoTT7pqqXvravtlCDsJYI5XgAWNfRB79bzDLkfZdYtNs_ZQVHdr8HLKh36waBbHJ8Zmc3_V9GvuOJK9gjCz1y2yn29PedT6HA2kf1n36NPMEh21ZmtwG7XrXbR-OHfV27gDYD82UT-lruIi0VYukKOlRNDGrwsnm2t5TAra8aaQ7V9NS3Y1EbyFvD6EAHHWFH_3ncNIzM2dJEcWSJmDae31LBHXIDOeaUq7MIYAqGB92nLwEP_i-JHUHRxBE2SRDe0To1mBgttmWNQUvPMO9Z3bmnzLM4W8TK7S7M2n8w3sRAN4q3NKS7gW4iUTqvqLvFRRotrxCIt6GE__s57hOk1jaXUgytkj-reuZS9rbsJ9bL0UwTAzvNf5u7orsPMTDzPSoGLto7_43 "SHOW_PERSON_OUTLINE() Sample")

## (C4 styled) Sequence diagram specific layout options

- **SHOW_ELEMENT_DESCRIPTIONS(?show)**: show or hide (hidden is default) all element/participant related descriptions
- **SHOW_FOOT_BOXES(?show)**: show or hide (hidden is default) all element/participant related foot boxes
- **SHOW_INDEX(?show)**: show or hide (hidden is default) the relationship (call) related index (sequence number)

show is defined with `$show=true` and hide is defined with `$show=false`

### SHOW_ELEMENT_DESCRIPTIONS(?show)

```plantuml
@startuml
!include <C4/C4_Sequence>

SHOW_ELEMENT_DESCRIPTIONS()

Person(admin, "Administrator", "People that administrates the products")
System_Boundary(c1, 'Sample')
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
' in a sequence diagram Boundary_End() has to be used instead of  { }
Boundary_End()
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![SHOW_ELEMENT_DESCRIPTIONS() Sample](https://www.plantuml.com/plantuml/png/VL9BIyD04BxlhnZheIcKYk9Hf3465MmDJTHZs6Qdkh2Fk3iX5F6_Ez7MwiMz3Cnyhnbs7aa6wgo1ylRXkSxlyqM-hEhhlCnMTqLrzx0iamcKqhO6nP5sZUaKmaLsVfATroM-TUWQl1JY7xOG1OReNIALrMuAetIeEbAGvCE86mLwbWTwbGJoCCJ8BOGsUDKr54SJKMuZeQslVEUK3DkaEPt2-3lSU2A0N-OTIUqm93skQzcsBF-CQqZRrkX6alPkSCoEfv2MnMoPLqm926UpKrWyPSCiDSRt4Rh8gO4yDDwsaY6sCwI7d5MlYJ00QOk6lOPaOz0E9CJTHK1f-HAaXNtKEdUA3_agloJNECWhvl0-Ke7V0Bp3X_YBtgsRqBSVHzivivzOeTaVzB3fO-GmN6-hgYWPDO1-fWUP6oIEqUDGDi7RtvGvEiNVuHC0 "SHOW_ELEMENT_DESCRIPTIONS() Sample")

### SHOW_FOOT_BOXES(?show)

```plantuml
@startuml SHOW_FOOT_BOXES() Sample
!include <C4/C4_Sequence>

SHOW_FOOT_BOXES()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample')
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
' in a sequence diagram Boundary_End() has to be used instead of  { }
Boundary_End()
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![SHOW_FOOT_BOXES() Sample](https://www.plantuml.com/plantuml/png/PL5BJy904BxlhnZnG4a81iFH64g3Sj4IMyLRCtG7tMGVTNURXXZ_krD1qRYNINQ-v-uyHFInDHgALRwfRlAyh1Rvyx98Hb2WQJI9CsLhtKg2gsnsaSsgWjvQiZLT2_6F8yIQV70sGMcK7SCWxQSAqMDqVZ0InJv4CjN2jLQYtoVrT0p3Wz5m981Fvcn4PSad7MqhR1fMsT0MqgRHgiQed1tmLNO-XhHOJ-wN9LCymULa2dTFMRzBjNPTW3Pm5eWEQcSQP8XfTLJi16MdOYGFKHdIx1KusH2K1OHmR0TIuOj70zzHgwML_2Yl-2MvfLvUCeVxe0It0tY73_4NVQoRn8CVHpiwivzu8FtzJAUcZu73y5oLvRfWL0_wsPvaRYboZ8xwiVFE_ARCoKh-q4y0 "SHOW_FOOT_BOXES() Sample")

### SHOW_INDEX(?show)

```plantuml
@startuml
!include <C4/C4_Sequence>

SHOW_INDEX()

Person(admin, "Administrator")
System_Boundary(c1, 'Sample')
    Container(web_app, "Web Application", "C#, ASP.NET Core 2.1 MVC", "Allows users to compare multiple Twitter timelines")
' in a sequence diagram Boundary_End() has to be used instead of  { }
Boundary_End()
System(twitter, "Twitter")

Rel(admin, web_app, "Uses", "HTTPS")
Rel(web_app, twitter, "Gets tweets from", "HTTPS")
@enduml
```

![SHOW_INDEX() Sample](https://www.plantuml.com/plantuml/png/LL5BJy904BxlhnZnG4a81iFH66ebua4ajegtPkWEkia-wkus3J7-TwSy1FSooSuttsDc6YBws1WD-VnvLJukxcVloG1oDBKcSQ5ifHj9S9DDhh99cTDNGxQYMo7EuK8ioGTd4vH6sI7qqgwg43r6vti3aMz395FUkSPAzDka6W-XlzVe3mJmovoDg2pvfALrYNNDB2jQGrhNMbKObRCz_iekXv3coz5YLl289xWUZU7fBUjwgTQk3T04zWBHGULCZGmnZOwAbQ1eLOpa8If3chK2E-k3ie0G3i50AlpmQE1ejPnPoVlun1tbcZfwoJES1oMu3S0t_8Z_w4FS9ExrsDf1cVN42-dZcav9NmERuJelYcNEg0xqrptHF51a6orrPUETEH-PafLynby0 "SHOW_INDEX() Sample")

