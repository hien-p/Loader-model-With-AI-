
Mình lấy một ví dụ khá thú vị là video về  Build a custom textures website for shoes with AI using DALL·E  and Three.js (React, Tailwind) [link](https://www.youtube.com/@codeandplay). Để phân tích mẩu xẻ từng dòng code. 

Để hiểu những dòng code  chúng mình sẽ phải học hiểu được các  cách [React hoạt động như thế nào?](https://react.dev/learn) . Ngoài ra cũng cần nắm [căn bản về việc Therejs](https://threejs.org/docs/index.html#manual/en/introduction/Creating-a-scene). 

Từ đó, ta có thể hiểu được nguyên lý hoạt động của thư viện Therejs 3D:
* Dùng Canvas để  set camera, shadows, gl. Ngoài ra mình cũng prefer  về cái này rằng ta nên  set các Object ở center( ngay giữa trung tâm để tiện về thiết kế). 
```js

// https://github.com/codeandplay/tutorials/blob/master/threejs-ai-texture/client/src/components/TextureChanger.jsx
function TextureChanger({ aiTexture }) {
    return (
        <Canvas
            shadows
            camera={{
                position: [8, 1.5, 8],
                fov: 25
            }}
            gl={{ preserveDrawingBuffer: true }}
        >
            <Center top position={[0, -1.05, 0]}>
                <Shoe aiTexture={aiTexture} />
            </Center>
        </Canvas >
    )
}
```

- Loader model by using function UseGLTF(): Tạo một object bằng function dưới đây. Tuy nhiên để cần phải hiểu về [mesh làm được gì và những ý nghĩa về các thông số ](https://threejs.org/docs/index.html#api/en/objects/Mesh) này thì mình xin phép nói sơ qua: 
	- [CastShadow](https://threejs.org/docs/#api/en/lights/DirectionalLight.castShadow): là tham số cho light create dynamic shadow(meaning Ánh sáng sẽ tạo ra bóng có thể cử động). Có type là  Boolean. Mặc định là false
	- Geometry: Trong Mesh là một sự kết hợp giũa geometry  và Material. Geometry giống như một khung sương vậy còn Material thì như lớp da bên ngoài. Ý nghĩa của GEO là tập hợp các điểm trong không gian, ta gọi là các đỉnh và kết nối lại với nó. 
	- Material: Ở trong therejs thì sẽ có nhiều các Material sẵn để có thể sử dụng. Ví dụ như  MeshStandardMaterial trong ví dụ là một material tiêu chuẩn hỗ trợ dễ config và tuỳ biến.  [material-roughness](https://threejs.org/docs/#api/en/materials/MeshStandardMaterial.roughness) cũng là một params trong đấy
	- Scale và Rotation  đều là những thông số về [object 3D](https://threejs.org/docs/#api/en/core/Object3D.scale) 
	* Decal Gemmetry: Có một câu hỏi là làm thế nào để user place text hoặc hình lên trên models?. Một trong cách tiếp cận đó là dùng [DecaL qua thư viện ](https://github.com/pmndrs/drei#decal). Ta có thể set up cơ bản như sau nếu muốn nhét hình vào obj. bạn có thể xem thêm  [ví dụ về việc có decal](https://threejs.org/examples/#webgl_decals)

```js

// example cho việc set up Decal trên hình 
import { Decal } from '@react-three/drei'

const Embroidery = ({ imageTexture }) => {
  return (
    <Decal
      scale={0.25}
      debug
      map={imageTexture}
      position={[0, 0, -0.1]}
    />
  )
}

export default Embroidery
```

còn đây là code chính cho toàn bộ những tham số mà mình giải thích trên 
```js
function Shoe({ aiTexture }) {
    const { nodes } = useGLTF('/shoe.glb')

    return (
        <group>
            <mesh
                castShadow
                geometry={nodes.Object_2.geometry}
                material={new THREE.MeshStandardMaterial()}
                material-roughness={4}
                scale={[15, -15, -15]}
                rotation={[-0.5, 0, -0.05]}
            >
                <Decal
                    position={[0, 0, 0]}
                    rotation={[0, 5, 0]}
                    scale={-0.3}
                    map={useTexture(aiTexture)}
                />
            </mesh>
        </group>
    )
}  

```
kết quả là ta sẽ thấy một model sau 
![[Pasted image 20231124014720.png]]


Để phủ bỏng một object thì mình sẽ cần AccumulativeShadows để phủ bóng dưới của một Obj. Ví dụ nhé. 

Về màu sắc thì bọn mình nhận thấy mình có thể đưa tất cả các object thành màu đen để tiện cho việc design sản phẩm. Cho phép người dùng tự custom color. Phần code tham khảo 


# TODO: 
- [x] Understading what a scene, a camera, mesh, geometry, material and demo about it
- [ ] Using text to image of Dall-e Model to interact with this library. 

# Target 
- [ ] build a app for Prompt Creator to design their own mockup products with 3D model. Users  can select form and custom the model
- [ ] Design Prototype like this:
[![WhatsApp Image 2023-02-25 at 17.15.21](https://global.discourse-cdn.com/standard17/uploads/threejs/optimized/3X/6/c/6c7e56c9f45c6b78e20b7c6f00cf6ab4587994fb_2_630x500.jpeg)](https://global.discourse-cdn.com/standard17/uploads/threejs/original/3X/6/c/6c7e56c9f45c6b78e20b7c6f00cf6ab4587994fb.jpeg "WhatsApp Image 2023-02-25 at 17.15.21")