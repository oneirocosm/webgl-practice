<canvas class="glcanvas" width="640" height="480"></canvas>

<script lang="ts">
    import * as webglUtils from "./webgl-utils";

    export let vertexShaderSource: string = `#version 300 es
 
    in vec2 a_position;

    uniform vec2 u_resolution;

    void main() {
        vec2 zeroToOne = a_position / u_resolution;
        vec2 zeroToTwo = zeroToOne * 2.0;
        vec2 clipSpace = zeroToTwo - 1.0;
        gl_Position = vec4(clipSpace, 0, 1);
    }
    `;
    export let fragmentShaderSource: string = `#version 300 es
    precision mediump float;
 
    out vec4 outColor;
 
    void main() {
      outColor = vec4(1, 0, 0.5, 1);
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
        let positionBuffer = gl.createBuffer();
        gl.bindBuffer(gl.ARRAY_BUFFER, positionBuffer);
        let positions = [
            10, 20,
            80, 20,
            10, 30,
            10, 30,
            80, 20,
            80, 30,
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
        gl.bindVertexArray(vao);
        gl.uniform2f(resolutionUniformLocation, gl.canvas.width, gl.canvas.height);
        let primitiveType = gl.TRIANGLES;
        let count = 6;
        gl.drawArrays(primitiveType, offset, count);
    }
    window.addEventListener("load", () =>{main()});
</script>