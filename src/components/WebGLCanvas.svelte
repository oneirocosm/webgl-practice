<canvas class="glcanvas" width="640" height="480"></canvas>

<script lang="ts">
    import * as webglUtils from "./webgl-utils";

    export let vertexShaderSource: string = `#version 300 es
 
    in vec2 a_position;

    void main() {
        gl_Position = vec4(a_position, 0, 1);
    }
    `;
    export let fragmentShaderSource: string = `#version 300 es
    precision mediump float;

    uniform vec2 u_resolution;
 
    out vec4 outColor;
 
    void main() {
        vec2 st = gl_FragCoord.xy/u_resolution;
        outColor = vec4(st, 0.0, 1.0);
    }
    `;
    function main() {
        const canvas = document.querySelector(".glcanvas") as HTMLCanvasElement;
        const gl = canvas.getContext("webgl2");

        if (!gl) {
            alert("Unable to initialize WebGL. Your browser may not support it.");
            return;
        }
        let vertexShader = webglUtils.createShader(gl, gl.VERTEX_SHADER, vertexShaderSource) as WebGLShader;
        let fragmentShader = webglUtils.createShader(gl, gl.FRAGMENT_SHADER, fragmentShaderSource) as WebGLShader;
        let program = webglUtils.createProgram(gl, vertexShader, fragmentShader) as WebGLProgram;
        let positionAttributeLocation = gl.getAttribLocation(program, "a_position");
        let resolutionUniformLocation = gl.getUniformLocation(program, "u_resolution");
        let timeLocation = gl.getUniformLocation(program, "u_time");
        let positionBuffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
        let positions = [
            -1.0, -1.0,
            1.0, -1.0,
            -1.0, 1.0,
            -1.0, 1.0,
            1.0, -1.0,
            1.0, 1.0,
        ];
        gl.bufferData(gl.ARRAY_BUFFER, new Float32Array(positions), gl.STATIC_DRAW);
        let vao = gl.createVertexArray();
        gl.bindVertexArray(vao);
        gl.enableVertexAttribArray(positionAttributeLocation);

        let size: number = 2;
        let type = gl.FLOAT;
        let normalize = false;
        let stride = 0;
        let offset = 0;
        gl.vertexAttribPointer(positionAttributeLocation, size, type, normalize, stride, offset);
        webglUtils.resizeCanvasToDisplaySize(gl.canvas as HTMLCanvasElement);
        gl.viewport(0, 0, gl.canvas.width, gl.canvas.height);

        gl.clearColor(0.0, 0.0, 0.0, 1.0);
        gl.clear(gl.COLOR_BUFFER_BIT | gl.DEPTH_BUFFER_BIT);

        gl.useProgram(program);
        gl.uniform2f(resolutionUniformLocation, gl.canvas.width, gl.canvas.height);
        gl.uniform1f(timeLocation, 1.0);

        let primitiveType = gl.TRIANGLES;
        let count = 6;
        gl.drawArrays(primitiveType, offset, count);
    }
    window.addEventListener("load", () =>{main()});
</script>