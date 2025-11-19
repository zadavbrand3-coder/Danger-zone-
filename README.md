# Danger-zone-
using UnityEngine;

[RequireComponent(typeof(Rigidbody))]
public class PlayerMovement : MonoBehaviour
{
    public float moveSpeed = 6f;
    public float sprintMultiplier = 1.6f;
    public float jumpForce = 6f;

    Rigidbody rb;
    Vector3 inputDir;
    bool wantJump;

    void Awake()
    {
        rb = GetComponent<Rigidbody>();
        rb.freezeRotation = true;
    }

    void Update()
    {
        // Get input (keyboard for editor; use touch/virtual stick on mobile)
        float h = Input.GetAxis("Horizontal");
        float v = Input.GetAxis("Vertical");
        bool sprint = Input.GetKey(KeyCode.LeftShift);

        inputDir = new Vector3(h, 0f, v);
        inputDir = Vector3.ClampMagnitude(inputDir, 1f);

        if (Input.GetButtonDown("Jump"))
            wantJump = true;
    }

    void FixedUpdate()
    {
        Vector3 worldMove = transform.TransformDirection(inputDir) * moveSpeed * (Input.GetKey(KeyCode.LeftShift) ? sprintMultiplier : 1f);
        Vector3 vel = rb.velocity;
        Vector3 targetVel = new Vector3(worldMove.x, vel.y, worldMove.z);
        rb.velocity = targetVel;

        if (wantJump && IsGrounded())
        {
            rb.AddForce(Vector3.up * jumpForce, ForceMode.VelocityChange);
        }
        wantJump = false;
    }

    bool IsGrounded()
    {
        return Physics.Raycast(transform.position + Vector3.up * 0.1f, Vector3.down, 0.15f);
    }
}
