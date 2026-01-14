using UnityEngine;

public class CarController : MonoBehaviour
{
    public float speed = 10f;
    public float turnSpeed = 50f;
    public float turboForce = 20f;
    public float flyForce = 8f;
    public GameObject missilePrefab;
    public Transform firePoint;

    private Rigidbody rb;

    void Start()
    {
        rb = GetComponent<Rigidbody>();
    }

    void FixedUpdate()
    {
        float move = Input.GetAxis("Vertical");
        float turn = Input.GetAxis("Horizontal");

        rb.AddForce(transform.forward * move * speed);
        transform.Rotate(0, turn * turnSpeed * Time.fixedDeltaTime, 0);
    }

    public void Turbo()
    {
        rb.AddForce(transform.forward * turboForce, ForceMode.Impulse);
    }

    public void Fly()
    {
        rb.AddForce(Vector3.up * flyForce, ForceMode.Impulse);
    }

    public void Fire()
    {
        Instantiate(missilePrefab, firePoint.position, firePoint.rotation);
    }
}
