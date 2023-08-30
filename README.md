uniform mat4 gbufferProjection;
//color.rgb = texture2DLod(shadowcolor, texcoord.st, 0).rgb * 1.0f;
//color.rgb = texture2DLod(shadowcolor1, texcoord.st, 0).aaa * 1.0f;
//color.rgb = vec3(texture2DLod(shadowtex1, texcoord.st, 0).x) * 1.0f;

//color.rgb = texture2D(gdepth, texcoord.st).bbb * 0.8 + 0.2;

//color.rgb = vec3(fwidth(GetDepthLinear(texcoord.st + vec2(0.5 / viewWidth, 0.5 / viewHeight)) + GetDepthLinear(texcoord.st - vec2(0.5 / viewWidth, 0.5 / viewHeight))));

// color.rgb += fwidth(color.rgb) * 0.5;

gl_FragColor = vec4(color.rgb, 1.0f);

}
surface.cloudShadow = 1.0f;
const float sunlightMult = 0.21f;

//Apply lightmaps to albedo and generate final shaded surface
vec3 finalComposite = final.sunlight * 0.9f * 1.5f * sunlightMult //Add direct sunlight
+ final.skylight * 0.03f //Add ambient skylight
+ final.nolight * 0.00003f //Add base ambient light
//+ final.bouncedSunlight * 0.005f * sunlightMult //Add fake bounced sunlight
+ final.scatteredSunlight * 0.02f * (1.0f - sunlightMult) //Add fake scattered sunlight
//+ final.scatteredUpLight * 0.0015f * sunlightMult
+ final.torchlight * 2.0f * TORCHLIGHT_BRIGHTNESS //Add light coming from emissive blocks
+ final.glow.lava * 1.6f * TORCHLIGHT_BRIGHTNESS
+ final.glow.glowstone * 1.1f * TORCHLIGHT_BRIGHTNESS
+ final.glow.fire * 0.025f * TORCHLIGHT_BRIGHTNESS
+ final.glow.torch * 0.15f * TORCHLIGHT_BRIGHTNESS
+ final.heldLight * 0.05f * TORCHLIGHT_BRIGHTNESS
;

delta.rgb *= mix(vec3(1.0), vec3(0.1, 0.3, 1.0) * 1.0, surface.mask.water);

//Apply sky to final composite
surface.sky.albedo *= 6.0f;
surface.sky.albedo = surface.sky.albedo * surface.sky.tintColor + surface.sky.sunglow + surface.sky.sunSpot;
//DoNightEye(surface.sky.albedo);
finalComposite += surface.sky.albedo; //Add sky to final image
finalComposite += delta.rgb * sunlightMult * 1.4;



//if eye is in water, do underwater fog
if (isEyeInWater > 0) {
finalComposite *= 9.0;
//CalculateUnderwaterFog(surface, finalComposite);
}
